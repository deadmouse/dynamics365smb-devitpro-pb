---
title: "SetValue Method"
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
# SetValue Method
Set the contents of the JsonValue variable to the JSON representation of the given value.


## Syntax
```
 JsonValue.SetValue(Value: Option)
```
## Parameters
*JsonValue*  
&emsp;Type: [JsonValue](jsonvalue-data-type.md)  
An instance of the [JsonValue](jsonvalue-data-type.md) data type.  

*Value*  
&emsp;Type: [Option](../option/option-data-type.md)  
  



[//]: # (IMPORTANT: END>DO_NOT_EDIT)

## Remarks
- When Value is a Char, Byte, Option, Integer type, the integral value will be stored and serialized as a JSON Number.
- The JsonValue will be disconnected from its current JSON tree and the data contained by the JsonValue will be replaced with the new value.

## See Also
[JsonValue Data Type](jsonvalue-data-type.md)  
[Getting Started with AL](../../devenv-get-started.md)  
[Developing Extensions](../../devenv-dev-overview.md)