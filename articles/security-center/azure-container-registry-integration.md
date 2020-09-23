---
title: Centro de Segurança Azure e Registo de Contentores Azure
description: Saiba a digitalização dos seus registos de contentores com o Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 1335b1034304b7efe2b113f7ff2d2927fea41638
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977358"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Digitalização da imagem do registo do contentor Azure pelo Centro de Segurança

O Registo de Contentores Azure (ACR) é um serviço de registo privado e gerido do Docker que armazena e gere as suas imagens de contentores para implantações do Azure num registo central. Baseia-se no registo 2.0 do Docker.

Ativar **o Azure Defender para registos** de contentores para uma visibilidade mais profunda sobre as vulnerabilidades das imagens nos registos baseados no Azure Resource Manager. Ative ou desative o plano ao nível da subscrição para cobrir todos os registos numa subscrição. Esta funcionalidade é carregada por imagem, como mostra a [página de preços](security-center-pricing.md). Ativando o Azure Defender, garante que o Centro de Segurança está pronto para digitalizar imagens que são empurradas para o registo. 


## <a name="when-are-images-scanned"></a>Quando as imagens são digitalizadas?

Sempre que uma imagem é empurrada para o seu registo, o Centro de Segurança digitaliza automaticamente essa imagem. Para ativar a digitalização de uma imagem, empurre-a para o seu repositório.

Quando a varredura termina (normalmente após aproximadamente 2 minutos, mas pode ser até 15 minutos), os resultados estão disponíveis como recomendações do Centro de Segurança como esta:

[![Amostra da recomendação do Centro de Segurança Azure sobre vulnerabilidades descobertas numa imagem hospedada do Registo de Contentores Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Benefícios da integração

O Security Center identifica registos ACR baseados em Recursos Azure na sua subscrição e fornece sem problemas:

* **Vulnerabilidade nativa do Azure procurando** todas as imagens de Linux empurradas. O Security Center verifica a imagem usando um scanner do fornecedor líder de pesquisa de vulnerabilidades da indústria, Qualys. Esta solução nativa é perfeitamente integrada por padrão.

* **Recomendações de segurança** para imagens Linux com vulnerabilidades conhecidas. O Centro de Segurança fornece detalhes de cada vulnerabilidade reportada e uma classificação de gravidade. Além disso, dá orientação para como remediar as vulnerabilidades específicas encontradas em cada imagem empurrada para o registo.

![Centro de Segurança Azure e Registo de Contentores Azure (ACR) visão geral de alto nível](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>FAQ para digitalização da imagem do registo do contentor de Azure

### <a name="how-does-security-center-scan-an-image"></a>Como é que o Centro de Segurança verifica uma imagem?
A imagem é retirada do registo. É então executado numa caixa de areia isolada com o scanner Qualys que extrai uma lista de vulnerabilidades conhecidas.

O Centro de Segurança filtra e classifica as descobertas do scanner. Quando uma imagem é saudável, o Centro de Segurança marca-a como tal. O Centro de Segurança gera recomendações de segurança apenas para imagens que têm problemas a resolver. Ao notificar apenas quando há problemas, o Centro de Segurança reduz o potencial de alertas informativos indesejados.

### <a name="how-often-does-security-center-scan-my-images"></a>Com que frequência o Centro de Segurança verifica as minhas imagens?
As imagens são ativadas em cada impulso.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da digitalização através da REST API?
Sim. Os resultados estão em [Sub-Avaliações Rest API](/rest/api/securitycenter/subassessments/list/). Além disso, pode utilizar o Azure Resource Graph (ARG), a API semelhante a Kusto para todos os seus recursos: uma consulta pode obter uma digitalização específica.
 
### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Que tipos de registo são digitalizados? Que tipos são cobrados?
A secção de disponibilidade lista os tipos de registos de contentores suportados pelo Azure Defender para registos de contentores. 

Se os registos que não forem suportados estiverem ligados à sua subscrição do Azure, não serão digitalizados e não serão cobrados por eles.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Kubernetes Service](azure-kubernetes-service-integration.md)


