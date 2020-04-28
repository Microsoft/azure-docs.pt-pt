---
title: Azure Analysis Services pseudónimo nomes de servidores / Microsoft Docs
description: Saiba como criar pseudónimos de servidor do Azure Analysis Services. Os utilizadores podem então ligar-se ao seu servidor com um nome de pseudónimo mais curto em vez do nome do servidor.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "73572280"
---
# <a name="alias-server-names"></a>Nomes de servidores de Alias

Ao utilizar um pseudónimo de nome de servidor, os utilizadores podem ligar-se ao seu servidor de Serviços de Análise Azure com um *pseudónimo* mais curto em vez do nome do servidor. Ao ligar-se a partir de uma aplicação do cliente, o pseudónimo é especificado como ponto final utilizando o formato **protocolo link://.** O ponto final devolve então o nome real do servidor para se ligar.

Os nomes dos servidores da Alias são bons para:

- Modelos de migração entre servidores sem afetar os utilizadores. 
- Os nomes amigáveis do servidor são mais fáceis de lembrar pelos utilizadores. 
- Direcione os utilizadores para diferentes servidores em diferentes horas do dia. 
- Utilizadores direcionados em diferentes regiões para casos geograficamente mais próximos, como quando se utiliza o Azure Traffic Manager. 

Qualquer ponto final HTTPS que deseja um nome válido do servidor do Azure Analysis Services pode servir como pseudónimo. O ponto final deve suportar HTTPS sobre a porta 443 e a porta não deve ser especificada no URI.

![Pseudónimos usando formato de ligação](media/analysis-services-alias/aas-alias-browser.png)

Ao ligar-se a partir de um cliente, o nome do servidor de pseudónimos é introduzido utilizando **link://** formato de protocolo. Por exemplo, no Power BI Desktop:

![Ligação de ambiente de trabalho Power BI](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Criar um pseudónimo

Para criar um ponto final de pseudónimo, pode utilizar qualquer método que derete um nome válido de servidor do Serviço de Análise Azure. Por exemplo, uma referência a um ficheiro no Armazenamento De Blob Azure contendo o nome real do servidor, ou criar e publicar uma aplicação ASP.NET Web Forms.

Neste exemplo, uma aplicação de formulários web ASP.NET é criada no Estúdio Visual. A referência da página principal e o controlo do utilizador são removidos da página Padrão.aspx. O conteúdo do Default.aspx é simplesmente a seguinte diretiva página:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

O evento Page_Load em Default.aspx.cs utiliza o método Response.Write() para devolver o nome do servidor dos Serviços de Análise Azure.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Consulte também

[Bibliotecas de clientes](analysis-services-data-providers.md)   
[Ligar a partir do Power BI Desktop](analysis-services-connect-pbi.md)
