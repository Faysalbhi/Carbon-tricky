## 📌 Handling Month Additions with Carbon

When adding months in PHP/Laravel with Carbon, there’s a well-known gotcha with end-of-month dates.

### 🔹 The Problem
```php
use Carbon\Carbon;

$date = Carbon::create(2025, 1, 31);

echo $date->copy()->addMonth();           // 2025-03-03 ❌ overflow
echo $date->copy()->addMonthNoOverflow(); // 2025-02-28 ✅ safe
```
addMonth() overflows into the next valid date (Jan 31 → Mar 3).

addMonthNoOverflow() safely caps at the end of the target month (Jan 31 → Feb 28).

### The Custom Solution

  To handle recurring billing or subscriptions, we want:
  
  If the original date is the last day of the month, the result should also be the last day of the next month.

Otherwise, just move to the same day in the next month.
```bash
  function nextMonthSameOrEnd(Carbon $date): Carbon
  {
      if ($date->isLastOfMonth()) {
          return $date->copy()->addMonthNoOverflow()->endOfMonth();
      }
  
      return $date->copy()->addMonthNoOverflow();
  }
```
### Examples
```bash
  Input Date	Result Date	Why
  2025-01-31 00:00:00	2025-02-28 23:59:59	Feb has only 28 days
  2025-03-31 00:00:00	2025-04-30 23:59:59	Apr ends on 30
  2025-05-31 00:00:00	2025-06-30 23:59:59	Jun ends on 30
  2025-01-15 00:00:00	2025-02-15 00:00:00	Middle of month stays same
```
