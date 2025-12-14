---
title: Generating new strategy
---
# Generating new strategy file

To get started, go the menu, and click on the "New Strategy" button:

![new-strategy-menu-button](https://api1.jesse.trade/storage/images/docs/new-strategy-menu-button.jpg)

Then, give it a name. For example:Inside bar

![new-strategy-form](https://api1.jesse.trade/storage/images/docs/new-strategy-form.jpg)

This will generate `AwesomeStrategy` class located at `jesse/strategies/AwesomeStrategy/__init__.py` including all the methods that are required to run the strategy:

```py
from jesse.strategies import Strategy
from jesse import utils

class InsideBarBreakout(Strategy):

    def should_long(self):
        return self.is_inside_bar() and self.price > self.mother_high()

    def should_short(self):
        return self.is_inside_bar() and self.price < self.mother_low()

    def go_long(self):
        qty = utils.size_to_qty(self.balance * 0.1, self.price)

        entry = self.mother_high()
        stop = self.mother_low()
        risk = entry - stop
        take_profit = entry + (risk * 2)

        self.buy = qty, entry
        self.stop_loss = qty, stop
        self.take_profit = qty, take_profit

    def go_short(self):
        qty = utils.size_to_qty(self.balance * 0.1, self.price)

        entry = self.mother_low()
        stop = self.mother_high()
        risk = stop - entry
        take_profit = entry - (risk * 2)

        self.sell = qty, entry
        self.stop_loss = qty, stop
        self.take_profit = qty, take_profit

    def is_inside_bar(self):
        current = self.candles[-1]
        previous = self.candles[-2]

        return (
            current[3] < previous[3] and
            current[4] > previous[4]
        )

    def mother_high(self):
        return self.candles[-2][3]

    def mother_low(self):
        return self.candles[-2][4]

    def should_cancel_entry(self):
        return False

