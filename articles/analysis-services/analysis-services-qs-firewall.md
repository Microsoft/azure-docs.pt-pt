---
title: Quickstart - Configure Azure Analysis Services firewall [ Microsoft Docs
description: Este quickstart ajuda-o a configurar uma firewall para um servidor de Serviços de Análise Azure utilizando o portal Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 68994f9b79af55b32527eed52bbc4e5866c89538
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79205164"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Início Rápido: Configurar a firewall do servidor – Portal

Este início rápido ajuda-o a configurar uma firewall para o servidor do Analysis Services do Azure. Ativar uma firewall e configurar os intervalos dos endereços IP apenas para os computadores que acedem ao seu servidor são uma parte importante para proteger o servidor e os dados.

## <a name="prerequisites"></a>Pré-requisitos

- Um servidor do Analysis Services na subscrição. Para obter mais informações, veja [Início Rápido: Criar um servidor – Portal](analysis-services-create-server.md) ou [Início Rápido: Criar um servidor – PowerShell](analysis-services-create-powershell.md)
- Intervalos de endereços IP de um ou mais computadores cliente (se necessário).
- Alguns cenários em que o Power BI Premium se conecta aos Serviços de Análise Azure, incluindo a importação de dados (atualização) e relatórios paginados, não são atualmente suportados mesmo quando o acesso ao Power BI está ativado. O cenário mais comum de utilização do Live Connect a partir do Power BI Premium é suportado. Todos os cenários power BI Pro são suportados.


## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure 

[Inscreva-se no portal](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Configurar uma firewall

1. Clique no servidor para abrir a página Descrição geral. 
2. Em **DEFINIÇÕES** > **Firewall** > **Ativar firewall**, clique em **.**
3. Para permitir o acesso do DirectQuery a partir do serviço Power BI, em **Permitir acesso a partir do Power BI**, clique em **Ativar**.  
4. (Opcional) Especifique um ou mais intervalos de endereços IP. Introduza um nome, um endereço IP de início e de fim para cada intervalo. O nome da regra da firewall deve ser limitado a 128 caracteres e só pode conter caracteres maiúsculos, caracteres minúsculos, números, sublinhado e hífen. Espaços brancos e outros personagens especiais não são permitidos.
5. Clique em **Guardar**.

     ![Definições de firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, elimine os intervalos de endereços IP ou desative a firewall.

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, aprendeu a configurar uma firewall para o servidor. Agora que tem o servidor protegido com uma firewall, pode adicionar um modelo de dados básicos de exemplo a partir do portal. Ter um modelo de exemplo é útil para saber como configurar as funções de base de dados do modelo e testar as ligações de cliente. Para obter mais informações, avance para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao servidor](analysis-services-create-sample-model.md)
