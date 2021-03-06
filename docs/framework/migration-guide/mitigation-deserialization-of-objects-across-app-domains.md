---
title: "缓解：跨应用程序域的对象反序列化 | Microsoft Docs"
ms.custom: 
ms.date: 03/30/2017
ms.prod: .net-framework
ms.reviewer: 
ms.suite: 
ms.technology:
- dotnet-clr
ms.tgt_pltfrm: 
ms.topic: article
ms.assetid: 30c2d66c-04a8-41a5-ad31-646b937f61b5
caps.latest.revision: 5
author: rpetrusha
ms.author: ronpet
manager: wpickett
translationtype: Human Translation
ms.sourcegitcommit: 9f5b8ebb69c9206ff90b05e748c64d29d82f7a16
ms.openlocfilehash: f22ffc11ba3bce4c568c67459995842c3c103b6b
ms.lasthandoff: 04/18/2017

---
# <a name="mitigation-deserialization-of-objects-across-app-domains"></a>缓解：跨应用程序域的对象的反序列化
有时，当一个应用程序使用具有不同应用程序基的两个或多个应用程序域时，如果尝试跨应用程序域在逻辑调用上下文中反序列化对象，则会引发异常。  
  
## <a name="diagnosing-the-issue"></a>诊断问题  
 以下情况将会引发这一问题：  
  
1.  一个应用程序使用具有不同应用程序基的两个或多个应用程序域。  
  
2.  通过调用 <xref:System.Runtime.Remoting.Messaging.LogicalCallContext.SetData%2A?displayProperty=fullName> 或 <xref:System.Runtime.Remoting.Messaging.CallContext.LogicalSetData%2A?displayProperty=fullName> 等方法，将一些类型显式添加到 <xref:System.Runtime.Remoting.Messaging.LogicalCallContext> 中。 这些类型未标记为可序列化并且未存储在全局程序集缓存中。  
  
3.  之后，在非默认应用程序域中运行的代码尝试从配置文件中读取值，或者使用 XML 来反序列化对象。  
  
4.  为了读取配置文件或反序列化对象，<xref:System.Xml.XmlReader> 对象尝试访问配置系统。  
  
5.  如果配置系统尚未初始化，则必须完成其初始化。 这意味着，运行时环境还必须为配置系统创建稳定的路径，它的作用如下：  
  
    1.  查找非默认应用程序域的证据。  
  
    2.  尝试根据默认应用程序域计算非默认应用程序域的证据。  
  
    3.  为获取默认应用程序域证据而进行的调用，会触发从非默认应用程序域到默认应用程序域的跨应用程序域调用。  
  
    4.  作为 .NET Framework 中跨应用程序域协定的一部分，逻辑调用上下文的内容必须跨应用程序域边界进行封送。  
  
6.  由于逻辑调用上下文中的类型不能在默认应用程序域中解析，因此将会引发异常。  
  
## <a name="mitigation"></a>缓解操作  
 若要解决此问题，请执行以下操作：  
  
1.  当引发异常时，查找调用堆栈中对 `get_Evidence` 的调用。 此异常可能是大部分异常中的任意一个，包括 <xref:System.IO.FileNotFoundException> 和 <xref:System.Runtime.Serialization.SerializationException>。  
  
2.  识别应用程序中没有向逻辑调用上下文添加应用程序的位置，然后添加以下代码：  
  
    ```  
    System.Configuration.ConfigurationManager.GetSection("system.xml/xmlReader");  
    ```  
  
## <a name="see-also"></a>另请参阅  
 [运行时更改](../../../docs/framework/migration-guide/runtime-changes-in-the-net-framework-4-5-1.md)
