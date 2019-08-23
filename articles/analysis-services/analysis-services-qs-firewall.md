---
title: Início Rápido – Configurar uma firewall para um servidor do Analysis Services no Azure | Microsoft Docs
description: Saiba como configurar uma firewall para uma instância de servidor do Analysis Services no Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 52ccaa17b9994751bb9dfd54848e20aef90146c3
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905888"
---
# <a name="quickstart-configure-server-firewall---portal"></a>Início rápido: Configurar a firewall do servidor - Portal

Este início rápido ajuda-o a configurar uma firewall para o servidor do Analysis Services do Azure. Ativar uma firewall e configurar os intervalos dos endereços IP apenas para os computadores que acedem ao seu servidor são uma parte importante para proteger o servidor e os dados.

## <a name="prerequisites"></a>Pré-requisitos

- Um servidor do Analysis Services na subscrição. Para saber mais, consulte [início rápido: Criar um portal](analysis-services-create-server.md) do servidor ou [início rápido: Criar um servidor-PowerShell](analysis-services-create-powershell.md)
- Intervalos de endereços IP de um ou mais computadores cliente (se necessário).
- Observe que, no momento, não há suporte para o cenário de importação de Power BI Premium.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure 

[Entrar no portal](https://portal.azure.com)

## <a name="configure-a-firewall"></a>Configurar uma firewall

1. Clique no servidor para abrir a página Descrição geral. 
2. Em **DEFINIÇÕES** > **Firewall** > **Ativar firewall**, clique em **Ativar**.
3. Para permitir o acesso do DirectQuery a partir do serviço Power BI, em **Permitir acesso a partir do Power BI**, clique em **Ativar**.  
4. (Opcional) Especifique um ou mais intervalos de endereços IP. Introduza um nome, um endereço IP de início e de fim para cada intervalo. O nome da regra de firewall deve ser limitado a 128 caracteres e só pode conter caracteres maiúsculos, caracteres minúsculos, números, sublinhados e hífen. Espaços em branco e outros caracteres especiais não são permitidos.
5. Clique em **Guardar**.

     ![Definições de firewall](./media/analysis-services-qs-firewall/aas-qs-firewall.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, elimine os intervalos de endereços IP ou desative a firewall.

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, aprendeu a configurar uma firewall para o servidor. Agora que tem o servidor protegido com uma firewall, pode adicionar um modelo de dados básicos de exemplo a partir do portal. Ter um modelo de exemplo é útil para saber como configurar as funções de base de dados do modelo e testar as ligações de cliente. Para obter mais informações, avance para o tutorial para adicionar um modelo de exemplo.

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um modelo de exemplo ao seu servidor](analysis-services-create-sample-model.md)
