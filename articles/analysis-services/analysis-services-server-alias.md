---
title: Azure Analysis Services pseudónimo nomes de servidores | Microsoft Docs
description: Saiba como criar pseudónimos de nome do servidor Azure Analysis Services. Os utilizadores podem então ligar-se ao seu servidor com um nome de pseudónimo mais curto em vez do nome do servidor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7fb52a0f03fa1f9ab9bc0c6a2c27adf70b4bf2ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491235"
---
# <a name="alias-server-names"></a>Nomes do servidor de pseudónimos

Ao utilizar um pseudónimo de nome de servidor, os utilizadores podem ligar-se ao servidor Azure Analysis Services com *um pseudónimo* mais curto em vez do nome do servidor. Ao ligar a partir de uma aplicação do cliente, o pseudónimo é especificado como um ponto final utilizando o formato de protocolo **link://.** Em seguida, o ponto final devolve o nome do servidor real para se ligar.

Os nomes do servidor alias são bons para:

- Migrar modelos entre servidores sem afetar os utilizadores. 
- Os nomes de servidores amigáveis são mais fáceis de lembrar para os utilizadores. 
- Direcione os utilizadores para diferentes servidores em diferentes horas do dia. 
- Direcione os utilizadores em diferentes regiões para casos geograficamente mais próximos, como quando se utiliza o Gestor de Tráfego do Azure. 

Qualquer ponto final HTTPS que devolva um nome válido do servidor Azure Analysis Services pode servir como um pseudónimo. O ponto final deve suportar HTTPS sobre a porta 443 e a porta não deve ser especificada no URI.

![Pseudónimo usando formato de link](media/analysis-services-alias/aas-alias-browser.png)

Ao ligar a partir de um cliente, o nome do servidor de pseudónimos é introduzido usando **link://** formato de protocolo. Por exemplo, no Power BI Desktop:

![Ligação de ambiente de trabalho Power BI](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Criar um pseudónimo

Para criar um ponto final de pseudónimo, pode utilizar qualquer método que devolva um nome de servidor de Serviços de Análise Azure válido. Por exemplo, uma referência a um ficheiro no Azure Blob Storage que contém o nome do servidor real, ou criar e publicar uma aplicação ASP.NET Web Forms.

Neste exemplo, uma aplicação de formulários web ASP.NET é criada em Visual Studio. A referência da página e o controlo do utilizador são removidos da página Padrão.aspx. O conteúdo do Predefinido.aspx são simplesmente a seguinte diretiva page:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

O Page_Load evento em Predefinido.aspx.cs utiliza o método Response.Write() para devolver o nome do servidor Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Ver também

[Bibliotecas de clientes](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)   
[Ligar a partir do Power BI Desktop](analysis-services-connect-pbi.md)