---
title: "CreateTotals Method"
ms.author: solsen
ms.custom: na
ms.date: 02/22/2019
ms.reviewer: na
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.service: "dynamics365-business-central"
author: solsen
---
[//]: # (START>DO_NOT_EDIT)
[//]: # (IMPORTANT:Do not edit any of the content between here and the END>DO_NOT_EDIT.)
[//]: # (Any modifications should be made in the .xml files in the ModernDev repo.)
# CreateTotals Method
Maintains totals for a variable in AL.


## Syntax
```
 Report.CreateTotals(var Var1: Decimal, [var Var2: Decimal,...])
```
## Parameters
*Report*  
&emsp;Type: [Report](report-data-type.md)  
An instance of the [Report](report-data-type.md) data type.  

*Var1*  
&emsp;Type: [Decimal](../decimal/decimal-data-type.md)  
Variable for which the system will maintain the total.
        
*Var2*  
&emsp;Type: [Decimal](../decimal/decimal-data-type.md)  
Variable for which the system will maintain the total.  



[//]: # (IMPORTANT: END>DO_NOT_EDIT)

## Remarks  
 CREATETOTALS maintains group and grand totals. The totals can be printed by placing controls that have the variable or variables that are the arguments of CREATETOTALS as their source expressions in the appropriate sections. The group totals are printed in GroupFooter sections, and the grand totals are printed in Footer sections.  
  
 This method is not supported on client report definition \(RDLC\) report layouts. In most cases, when you create a layout suggestion for a Classic report layout that uses the CREATETOTALS method, a SUM expression is created instead and no action is required.  
  
## Example  
 This example shows how to use the CREATETOTALS method to maintain totals for the two variables Amount and Quantity.  
  
```  
CurrReport.CREATETOTALS(Amount, Quantity);  
```  

## See Also
[Report Data Type](report-data-type.md)  
[Getting Started with AL](../../devenv-get-started.md)  
[Developing Extensions](../../devenv-dev-overview.md)