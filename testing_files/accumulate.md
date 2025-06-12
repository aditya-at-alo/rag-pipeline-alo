# ACCUMULATE Statement

Calculates one or more aggregate values of an expression during the iterations of a block. Use the `ACCUM` function to access the result of this accumulation.

## Syntax
```abl
ACCUMULATE { expression ( aggregate-phrase ) } ...
```
***expression***
An expression for which you want to calculate the aggregate value. The expression you use in the ACCUMULATE statement and the expression you use in the ACCUM function (when using the result of the ACCUMULATE statement) must be in exactly the same form. (For example, "A * B" and "B * A" are not in exactly the same form.)

***aggregate-phrase***
Identifies one or more values to calculate based on a change in expression or a break group. This is the syntax for aggregate-phrase:
### Syntax
```js
{ AVERAGE | COUNT | MAXIMUM | MINIMUM | TOTAL | SUB-AVERAGE 
  | SUB-COUNT | SUB-MAXIMUM | SUB-MINIMUM | SUB-TOTAL }...
  [ BY break-group]...
```
For more information, see the Aggregate phrase reference entry.

## Examples

This procedure calculates and displays statistics for all customers, but does not show the detail for each customer.

**r-acmIt.p**

```abl
FOR EACH Customer NO-LOCK:
  ACCUMULATE Customer.CreditLimit (AVERAGE COUNT MAXIMUM).END.

DISPLAY "MAX-CREDIT STATISTICS FOR ALL CUSTOMERS:" SKIP(2)
        "AVERAGE =" (ACCUM AVERAGE Customer.CreditLimit) SKIP(1)
        "MAXIMUM =" (ACCUM MAXIMUM Customer.CreditLimit) SKIP(1)
        "NUMBER OF CUSTOMERS =" (ACCUM COUNT Customer.CreditLimit) SKIP(1)
        WITH NO-LABELS.
```

The following procedure lists each item with its inventory value and lists that value as a percentage of the total inventory value of all items; it sorts items by highest value.

**r-acmlt2.p**

```abl
FOR EACH Item NO-LOCK:
  ACCUMULATE Item.OnHand * Item.Price (TOTAL).
END.

FOR EACH Item NO-LOCK BY Item.OnHand * Item.Price DESCENDING:
  DISPLAY Item.ItemNum Item.OnHand Item.Price Item.OnHand * Item.Price 
    LABEL "Value" 100 * (Item.OnHand * Item.Price) / 
    (ACCUM TOTAL Item.OnHand * Item.Price) LABEL "Value %".
END.
```

The following procedure displays all customers, sorted by salesrep and country within the list for each salesrep. The procedure calculates the balance for each customer, total balance for each country, and total balance for each salesrep.

**r-acc.p**

```abl
FOR EACH Customer NO-LOCK BREAK BY Customer.SalesRep BY Customer.Country:
  ACCUMULATE Customer.Balance 
    (TOTAL BY Customer.SalesRep BY Customer.Country).
  DISPLAY Customer.SalesRep WHEN FIRST-OF(Customer.SalesRrep) 
    Customer.Country Customer.Name Customer.Balance.
  IF LAST-OF(Customer.Country) THEN
    DISPLAY ACCUM TOTAL BY Customer.Country Customer.Balance
      COLUMN-LABEL "Country!Total".
  IF LAST-OF(Customer.SalesRep) THEN DO:
    DISPLAY Customer.SalesRep ACCUM TOTAL BY Customer.SalesRep
      Customer.Balance COLUMN-LABEL "SalesRep!Total".
    DOWN 1.
  END.
END.
```

## Note
You can use the ACCUMULATE statement only in blocks with the implicit looping property. ABL automatically supplies looping services to REPEAT and FOR EACH blocks. See Develop ABL Applications for more information on block properties.

## See also
ACCUM function, Aggregate phrase, AGGREGATE statement
