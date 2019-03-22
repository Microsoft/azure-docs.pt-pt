---
title: Gestão da região no Azure Stack | Documentos da Microsoft
description: Descrição geral da gestão da região no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: 29e4bb23b4b80f6e472d2dcb27fe5e5279a35890
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337719"
---
# <a name="region-management-in-azure-stack"></a>Gestão da região no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack usa o conceito de *regiões*, que são entidades lógicas compostas os recursos de hardware que compõem a infraestrutura do Azure Stack. Na gestão de região, pode encontrar todos os recursos necessários para operar com êxito a infraestrutura do Azure Stack.

Integrada uma implementação do sistema (conhecido como um *cloud do Azure Stack*) constitui uma única região. Cada ferramenta do Azure Stack Development Kit (ASDK) tem uma região, com o nome **local**. Se implementar um segundo sistema integrado do Azure Stack ou configurar outra instância do kit de desenvolvimento num hardware separado, esta nuvem do Azure Stack é uma região diferente.

## <a name="information-available-through-the-region-management-tile"></a>Informações disponíveis por meio do mosaico de gestão da região

O Azure Stack tem um conjunto de capacidades de gestão de região disponíveis na **gestão da região** mosaico. Este mosaico não está disponível para um operador do Azure Stack no dashboard predefinido no portal do administrador. Através deste mosaico, pode monitorizar e atualizar a sua região do Azure Stack e os respetivos componentes, que são específicos de cada região.

![O mosaico de gestão da região](media/azure-stack-region-management/image1.png)

Se clicar numa região no **gestão da região** mosaico, pode acessar as seguintes informações:

[![Descrição de painéis no painel de gestão da região](media/azure-stack-region-management/regionssm.png "painel de gestão da região")](media/azure-stack-region-management/regions.png#lightbox)

1. **Menu de recursos**. Acessar áreas de gerenciamento de infra-estrutura específicas e ver e gerir recursos de utilizador, como contas de armazenamento e redes virtuais.

2. **Alertas**. Liste os alertas de todo o sistema e fornecem detalhes sobre cada um dos alertas.

3. **Atualizações**. Ver a versão atual da sua infraestrutura do Azure Stack, as atualizações disponíveis e o histórico de atualização. Também pode atualizar o seu sistema integrado.

4. **Fornecedores de recursos**. Gerir a funcionalidade de utilizador fornecida pelos componentes necessários para executar o Azure Stack. Cada fornecedor de recursos é fornecido com uma experiência administrativa. Esta experiência pode incluir alertas para o fornecedor específico, métricas e outras capacidades de gestão específicas para o fornecedor de recursos.

5. **Funções de infraestrutura**. Os componentes necessários para executar o Azure Stack. Apenas as funções de infraestrutura que reportam os alertas são listadas. Ao selecionar uma função, pode ver os alertas associados a função e as instâncias de função onde esta função está em execução.

6. **propriedades**. O estado de registo e os detalhes do seu ambiente no painel de gestão da região. O estado pode ser **registada** ou **não registado**. Se registrado, ela também mostra o ID de subscrição do Azure que utilizou para registar o Azure Stack, juntamente com o grupo de recursos de registo e o nome.

## <a name="next-steps"></a>Passos Seguintes

- [Monitor health and alerts in Azure Stack](azure-stack-monitor-health.md) (Monitorizar o estado de funcionamento e os alertas no Azure Stack)
- [Gerir atualizações no Azure Stack](azure-stack-updates.md)
