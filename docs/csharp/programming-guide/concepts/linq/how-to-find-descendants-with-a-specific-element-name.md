---
title: "如何：查找具有特定元素名称的后代 (C#) | Microsoft Docs"
ms.custom: 
ms.date: 2015-07-20
ms.prod: .net
ms.reviewer: 
ms.suite: 
ms.technology:
- devlang-csharp
ms.topic: article
dev_langs:
- CSharp
ms.assetid: f684da20-bee9-47f5-9607-7e3fd7e67470
caps.latest.revision: 3
author: BillWagner
ms.author: wiwagn
translationtype: Human Translation
ms.sourcegitcommit: a06bd2a17f1d6c7308fa6337c866c1ca2e7281c0
ms.openlocfilehash: ef272c973d469376a9a7de85b8e90a955af08dfb
ms.lasthandoff: 03/13/2017


---
# <a name="how-to-find-descendants-with-a-specific-element-name-c"></a>如何：查找具有特定元素名称的后代 (C#)
有时，您想要查找所有具有特定名称的子代。 可以编写代码用于循环访问所有后代，但使用 <xref:System.Xml.Linq.XContainer.Descendants%2A> 轴更简单。  
  
## <a name="example"></a>示例  
 下面的示例演示如何根据元素名称查找子代。  
  
```csharp  
XElement root = XElement.Parse(@"<root>  
  <para>  
    <r>  
      <t>Some text </t>  
    </r>  
    <n>  
      <r>  
        <t>that is broken up into </t>  
      </r>  
    </n>  
    <n>  
      <r>  
        <t>multiple segments.</t>  
      </r>  
    </n>  
  </para>  
</root>");  
IEnumerable<string> textSegs =  
    from seg in root.Descendants("t")  
    select (string)seg;  
  
string str = textSegs.Aggregate(new StringBuilder(),  
    (sb, i) => sb.Append(i),  
    sp => sp.ToString()  
);  
  
Console.WriteLine(str);  
```  
  
 此代码生成以下输出：  
  
```  
Some text that is broken up into multiple segments.  
```  
  
## <a name="example"></a>示例  
 下面的示例演示如何对命名空间中的 XML 进行同样的查询。 有关详细信息，请参阅[使用 XML 命名空间 (C#)](../../../../csharp/programming-guide/concepts/linq/working-with-xml-namespaces.md)。  
  
```csharp  
XElement root = XElement.Parse(@"<root xmlns='http://www.adatum.com'>  
  <para>  
    <r>  
      <t>Some text </t>  
    </r>  
    <n>  
      <r>  
        <t>that is broken up into </t>  
      </r>  
    </n>  
    <n>  
      <r>  
        <t>multiple segments.</t>  
      </r>  
    </n>  
  </para>  
</root>");  
XNamespace ad = "http://www.adatum.com";  
IEnumerable<string> textSegs =  
    from seg in root.Descendants(ad + "t")  
    select (string)seg;  
  
string str = textSegs.Aggregate(new StringBuilder(),  
    (sb, i) => sb.Append(i),  
    sp => sp.ToString()  
);  
  
Console.WriteLine(str);  
```  
  
 此代码生成以下输出：  
  
```  
Some text that is broken up into multiple segments.  
```  
  
## <a name="see-also"></a>请参阅  
 <xref:System.Xml.Linq.XContainer.Descendants%2A>   
 [基本查询 (LINQ to XML) (C#)](../../../../csharp/programming-guide/concepts/linq/basic-queries-linq-to-xml.md)
