---
title: Centro de Segurança Azure e Registo de Contentores Azure
description: Conheça a integração do Azure Security Center com o Registo de Contentores Azure
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: 4fb623a77fc97fea1584451b73eef930c725f8a1
ms.sourcegitcommit: 581aaca8956b1717b7bc1c1d7710c782c22e6320
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517427"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integração do Registo de Contentores Azure com o Centro de Segurança

O Registo de Contentores Azure (ACR) é um serviço de registo privado e gerido do Docker que armazena e gere as suas imagens de contentores para implantações do Azure num registo central. Baseia-se no registo 2.0 do Docker.

Se estiver no nível padrão do Centro de Segurança Azure, pode adicionar o pacote de registos de contentores. Esta funcionalidade opcional traz uma visibilidade mais profunda às vulnerabilidades das imagens nos seus registos baseados na ARM. Ative ou desative o pacote ao nível de subscrição para cobrir todos os registos numa subscrição. Esta funcionalidade é carregada por imagem, como mostra a [página de preços](security-center-pricing.md). Ativando o pacote de registos de contentores, garante que o Centro de Segurança está pronto para digitalizar imagens que são empurradas para o registo. 

Sempre que uma imagem é empurrada para o seu registo, o Centro de Segurança digitaliza automaticamente essa imagem. Para ativar a digitalização de uma imagem, empurre-a para o seu repositório.

Quando a varredura termina (normalmente após aproximadamente 10 minutos, mas pode ser até 40 minutos), os resultados estão disponíveis como recomendações do Centro de Segurança como esta:

[![Amostra da recomendação do Centro de Segurança Azure sobre vulnerabilidades descobertas numa imagem hospedada do Registo de Contentores Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Benefícios da integração

O Security Center identifica registos ACR baseados na ARM na sua subscrição e fornece perfeitamente:

* **Vulnerabilidade nativa do Azure procurando** todas as imagens de Linux empurradas. O Security Center verifica a imagem usando um scanner do fornecedor líder de pesquisa de vulnerabilidades da indústria, Qualys. Esta solução nativa é perfeitamente integrada por padrão.

* **Recomendações de segurança** para imagens Linux com vulnerabilidades conhecidas. O Centro de Segurança fornece detalhes de cada vulnerabilidade reportada e uma classificação de gravidade. Além disso, dá orientação para como remediar as vulnerabilidades específicas encontradas em cada imagem empurrada para o registo.

![Centro de Segurança Azure e Registo de Contentores Azure (ACR) visão geral de alto nível](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Proteção de Máquinas Virtuais](security-center-virtual-machine-protection.md) - Descreve as recomendações do Centro de Segurança
