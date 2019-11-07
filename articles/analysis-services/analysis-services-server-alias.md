---
title: Azure Analysis Services nomes de servidor de alias | Microsoft Docs
description: Saiba como criar Azure Analysis Services aliases de nome de servidor. Os usuários podem, então, se conectar ao seu servidor com um nome de alias mais curto em vez do nome do servidor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572280"
---
# <a name="alias-server-names"></a>Nomes de servidor de alias

Usando um alias de nome de servidor, os usuários podem se conectar ao seu servidor de Azure Analysis Services com um *alias* mais curto em vez do nome do servidor. Ao se conectar de um aplicativo cliente, o alias é especificado como um ponto de extremidade usando o formato de protocolo **link://** . Em seguida, o ponto de extremidade retorna o nome do servidor real para se conectar.

Os nomes de servidor de alias são bons para:

- Migrar modelos entre servidores sem afetar os usuários. 
- Nomes de servidor amigáveis são mais fáceis para os usuários se lembrarem. 
- Encaminhe os usuários para servidores diferentes em momentos diferentes do dia. 
- Encaminhe os usuários em regiões diferentes para as instâncias que estão geograficamente mais próximas, como ao usar o Gerenciador de tráfego do Azure. 

Qualquer ponto de extremidade HTTPS que retorna um nome de servidor Azure Analysis Services válido pode servir como um alias. O ponto de extremidade deve oferecer suporte a HTTPS pela porta 443 e a porta não deve ser especificada no URI.

![Alias usando o formato de link](media/analysis-services-alias/aas-alias-browser.png)

Ao conectar-se de um cliente, o nome do servidor de alias é inserido usando o formato de protocolo **link://** . Por exemplo, em Power BI Desktop:

![Power BI Desktop conexão](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Criar um alias

Para criar um ponto de extremidade de alias, você pode usar qualquer método que retorne um nome de servidor de Azure Analysis Services válido. Por exemplo, uma referência a um arquivo no armazenamento de BLOBs do Azure que contém o nome do servidor real, ou criar e publicar um aplicativo de Web Forms ASP.NET.

Neste exemplo, um aplicativo ASP.NET Web Forms é criado no Visual Studio. A referência de página mestra e o controle de usuário são removidos da página default. aspx. O conteúdo de default. aspx é simplesmente a seguinte diretiva de página:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

O evento Page_Load em Default.aspx.cs usa o método Response. Write () para retornar o nome do servidor Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Consultar também

[Bibliotecas de cliente](analysis-services-data-providers.md)   
[Conectar de Power BI Desktop](analysis-services-connect-pbi.md)
