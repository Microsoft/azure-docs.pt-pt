---
title: Gerencie as extensões linguísticas no seu cluster Azure Data Explorer.
description: Utilize a extensão linguística para integrar outras línguas dentro das suas consultas do Azure Data Explorer KQL.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522475"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Gerir extensões de idiomas no seu cluster Azure Data Explorer (Pré-visualização)

A funcionalidade de extensões linguísticas permite-lhe utilizar plugins de extensão de idiomas para integrar outros idiomas nas suas consultas KQL do Explorador de Dados do Azure. Quando executa uma função definida pelo utilizador (UDF) utilizando um script relevante, o script obtém dados tabular como sua entrada e espera-se que produza saída tabular. O tempo de funcionamento do plugin está hospedado numa caixa de [areia,](/azure/kusto/concepts/sandboxes)um ambiente isolado e seguro, funcionando nos nós do cluster. Neste artigo, gere as extensões linguísticas plugin no seu cluster Azure Data Explorer dentro do portal Azure.

> [!NOTE]
> As extensões linguísticas do Azure Data Explorer que são atualmente suportadas são Python e R.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* Criar [um cluster azure Data Explorer e base de dados.](create-cluster-database-portal.md)

## <a name="enable-language-extensions-on-your-cluster"></a>Ativar extensões linguísticas no seu cluster

> [!WARNING]
> Por favor, reveja as [limitações](#limitations) antes de permitir uma extensão da linguagem.

Faça os seguintes passos para permitir extensões linguísticas no seu cluster:

1. No portal Azure, vá ao seu cluster Azure Data Explorer. 
1. Em **Configurações,** selecione **Configurações**. 
1. No painel de **Configurações,** selecione **On** para ativar uma extensão de idioma.
1. Selecione **Guardar**.
 
    ![extensão da linguagem em](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Ativar o processo de extensão da linguagem pode demorar alguns minutos. Durante esse tempo, o seu aglomerado será suspenso.
 
## <a name="run-language-extension-integrated-queries"></a>Executar consultas integradas em extensão linguística

* Saiba como [executar consultas Integradas de KQL](/azure/kusto/query/pythonplugin)da Python.
* Aprenda a [executar consultas KQL integradas r.](/azure/kusto/query/rplugin) 

## <a name="disable-language-extensions-on-your-cluster"></a>Desative as extensões linguísticas no seu cluster

> [!NOTE]
> Desativar as extensões linguísticas pode levar alguns minutos.

Faça os seguintes passos para desativar as extensões linguísticas no seu cluster:

1. No portal Azure, vá ao seu cluster Azure Data Explorer. 
1. Em **Configurações,** selecione **Configurações**. 
1. No painel de **Configurações,** selecione **Off** para desativar uma extensão de idioma.
1. Selecione **Guardar**.

    ![Extensão linguística fora](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Limitações

* A funcionalidade de extensões de idiomas não suporta a [encriptação do Disco](manage-cluster-security.md). 
* As extensões linguísticas runtime sandbox alocam espaço de disco mesmo que nenhuma consulta seja executada no âmbito da linguagem relevante.

