---
title: Criar um ficheiro. odc para ligar a um servidor Azure Analysis Services | Documentos da Microsoft
description: Saiba como criar um ficheiro de ligação de dados do Office para ligar e obter dados a partir de um servidor de Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 37f068be544f964f3aec63d85702098c8f382ab8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60785715"
---
# <a name="create-an-office-data-connection-file"></a>Crie um ficheiro de ligação de dados do Office

Informações deste artigo descrevem como pode criar um ficheiro de ligação de dados do Office para ligar a um servidor de Azure Analysis Services a partir do Excel 2016, o número de versão 16.0.7369.2117 ou anterior, ou o Excel 2013. Um atualizada [MSOLAP.7 fornecedor](analysis-services-data-providers.md) também é necessário.


1. Copie o ficheiro de ligação de exemplo abaixo e cole num editor de texto. 

2. No `odc:ConnectionString`, alterar as propriedades seguintes:

    *   Na `Data Source=asazure://<region>.asazure.windows.net/<servername>;` alterar `<region>` para a região do seu servidor do Analysis Services e `<servername>` para o nome do seu servidor.

    *   Na `Initial Catalog=<database>;` alterar `<database>` para o nome da base de dados.

3. Na `<odc:CommandText>Model</odc:CommandText>` alterar `Model` ao nome do seu modelo ou perspetiva. 

4. Guarde o ficheiro com um `.odc` extensão para o C:\Users\\*username*\Documents\My pasta de origens de dados.

5. O ficheiro com o botão direito e, em seguida, clique em **abrir no Excel**. Ou no Excel, sobre o **dados** Friso, clique em **ligações existentes**, selecione o ficheiro e, em seguida, clique em **aberto**.



**Ficheiro de ligação de exemplo**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="https://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="https://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="https://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```



