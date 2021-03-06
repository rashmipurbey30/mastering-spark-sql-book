# WindowsSubstitution Logical Evaluation Rule

`WindowsSubstitution` is a link:spark-sql-catalyst-Rule.adoc[logical evaluation rule] (i.e. `Rule[LogicalPlan]`) that the link:spark-sql-Analyzer.adoc[logical query plan analyzer] uses to resolve (_aka_ substitute) link:spark-sql-LogicalPlan-WithWindowDefinition.adoc[WithWindowDefinition] unary logical operators with <<spark-sql-Expression-UnresolvedWindowExpression.adoc#, UnresolvedWindowExpression>> to their corresponding link:spark-sql-Expression-WindowExpression.adoc[WindowExpression] with resolved link:spark-sql-Expression-WindowSpecDefinition.adoc[WindowSpecDefinition].

`WindowsSubstitution` is part of link:spark-sql-Analyzer.adoc#Substitution[Substitution] fixed-point batch of rules.

NOTE: It _appears_ that `WindowsSubstitution` is exclusively used for pure SQL queries because link:spark-sql-LogicalPlan-WithWindowDefinition.adoc[WithWindowDefinition] unary logical operator is created exclusively when `AstBuilder` link:spark-sql-LogicalPlan-WithWindowDefinition.adoc#creating-instance[parses window definitions].

If a window specification is not found, `WindowsSubstitution` fails analysis with the following error:

```
Window specification [windowName] is not defined in the WINDOW clause.
```

NOTE: The analysis failure is unlikely to happen given `AstBuilder` link:spark-sql-AstBuilder.adoc#withWindows[builds a lookup table of all the named window specifications] defined in a SQL text and reports a `ParseException` when a `WindowSpecReference` is not available earlier.

For every `WithWindowDefinition`, `WindowsSubstitution` takes the `child` logical plan and transforms its <<spark-sql-Expression-UnresolvedWindowExpression.adoc#, UnresolvedWindowExpression>> expressions to be a link:spark-sql-Expression-WindowExpression.adoc[WindowExpression] with a window specification from the `WINDOW` clause (see link:spark-sql-Expression-WindowExpression.adoc#WithWindowDefinition-example[WithWindowDefinition Example]).
