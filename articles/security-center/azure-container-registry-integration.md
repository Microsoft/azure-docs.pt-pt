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
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205998"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integração do Registo de Contentores Azure com o Centro de Segurança

O Registo de Contentores Azure (ACR) é um serviço de registo privado e gerido do Docker que armazena e gere as suas imagens de contentores para implantações azure num registo central. Baseia-se no registo 2.0 do Docker.

Se estiver no nível padrão do Azure Security Center, pode adicionar o pacote de Registos de Contentores. Esta funcionalidade opcional traz uma visibilidade mais profunda às vulnerabilidades das imagens nos seus registos baseados em ARM. Ative ou desative o pacote ao nível da subscrição para cobrir todos os registos numa subscrição. Esta funcionalidade é carregada por imagem, como mostra a página de [preços](security-center-pricing.md). Habilitando o pacote de Registos de Contentores, garante que o Centro de Segurança está pronto para digitalizar imagens que são empurradas para o registo. 

Sempre que uma imagem é empurrada para o seu registo, o Security Center digitaliza automaticamente essa imagem. Para desencadear a tomografia de uma imagem, empurre-a para o seu repositório.

Quando a varredura termina (normalmente após aproximadamente 10 minutos), os resultados estão disponíveis em recomendações do Centro de Segurança como esta:

[![Recomendação do Centro de Segurança Azure sobre vulnerabilidades descobertas num Registo de Contentores Azure (ACR) hospedada imagem](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Benefícios da integração

O Security Center identifica registos ACR baseados em ARM na sua subscrição e fornece sem problemas:

* **Vulnerabilidade nativa de Azure à procura** de todas as imagens do Linux empurradas. O Security Center digitaliza a imagem usando um scanner do fornecedor de vulnerabilidadelíder da indústria, qualys. Esta solução nativa é perfeitamente integrada por padrão.

* **Recomendações de segurança** para imagens linux com vulnerabilidades conhecidas. O Security Center fornece detalhes de cada vulnerabilidade reportada e uma classificação de gravidade. Além disso, dá orientações para como remediar as vulnerabilidades específicas encontradas em cada imagem empurradas para o registo.

![Visão geral de alto nível do Azure Security Center e do Registo de Contentores De Azure (ACR)](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança de contentores do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Proteção virtual de máquinas](security-center-virtual-machine-protection.md) - Descreve as recomendações do Centro de Segurança
