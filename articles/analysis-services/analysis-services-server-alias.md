---
title: Nomes de servidor de alias do Analysis Services do Azure | Documentos da Microsoft
description: Descreve como criar e utilizar os aliases de nome de servidor.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 66e6b4713591f099769543a75dcddec34f3d2e2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534322"
---
# <a name="alias-server-names"></a>Alias server names

Ao utilizar um alias de nome de servidor, os utilizadores podem ligar ao seu servidor Azure Analysis Services com uma menor *alias* em vez do nome de servidor. Ao ligar-se de um aplicativo cliente, o alias é especificado como um ponto final a utilizar o **ligação: / /** formato de protocolo. O ponto final, em seguida, devolve o nome do servidor real para se ligar.

Alias server names are good for:

- Modelos de migrar entre servidores sem afetar os utilizadores. 
- Nomes de servidor amigável são mais fáceis para os usuários se lembrem. 
- Direcionar os utilizadores para diferentes servidores em diferentes momentos do dia. 
- Direcionar os utilizadores em diferentes regiões com as instâncias que estão geograficamente mais próximos, como quando utilizar o Gestor de tráfego do Azure. 

Qualquer ponto de final de HTTPS, que retorna um nome de servidor válido do Azure Analysis Services pode servir como um alias. O ponto final tem de suportar HTTPS através da porta 443 e a porta não pode ser especificada no URI.

![Alias através do formato de ligação](media/analysis-services-alias/aas-alias-browser.png)

Ao ligar-se de um cliente, o nome do servidor de alias é inserido usando **ligação: / /** formato de protocolo. Por exemplo, no ambiente de trabalho do Power BI:

![Ligação do Power BI Desktop](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Criar um alias

Para criar um ponto de extremidade de alias, pode utilizar qualquer método que retorna um nome de servidor Azure Analysis Services válido. Por exemplo, uma referência a um ficheiro no armazenamento de Blobs do Azure que contém o servidor real dê um nome, ou criar e publicar uma aplicação de Web Forms do ASP.NET.

Neste exemplo, um aplicativo do ASP.NET Web Forms é criado no Visual Studio. O controlo de utilizador e de referência de página mestra são removidos da página default. aspx. O conteúdo de default. aspx é simplesmente a diretiva da página seguinte:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

O evento Page_Load no Default.aspx.cs usa o método Response para retornar o nome do servidor Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Consulte também

[Bibliotecas de cliente](analysis-services-data-providers.md)   
[Ligar a partir do Power BI Desktop](analysis-services-connect-pbi.md)
