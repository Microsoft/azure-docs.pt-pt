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
ms.openlocfilehash: f3ef633ff0271d74eea7320faadf17685976d3b6
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970472"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Integração do Registo de Contentores Azure com o Centro de Segurança

O Registo de Contentores Azure (ACR) é um serviço de registo privado e gerido do Docker que armazena e gere as suas imagens de contentores para implantações do Azure num registo central. Baseia-se no registo 2.0 do Docker.

Se estiver no nível padrão do Centro de Segurança Azure, pode adicionar o pacote de registos de contentores. Esta funcionalidade opcional traz uma visibilidade mais profunda às vulnerabilidades das imagens nos seus registos baseados na ARM. Ative ou desative o pacote ao nível de subscrição para cobrir todos os registos numa subscrição. Esta funcionalidade é carregada por imagem, como mostra a [página de preços](security-center-pricing.md). Ativando o pacote de registos de contentores, garante que o Centro de Segurança está pronto para digitalizar imagens que são empurradas para o registo. 


## <a name="availability"></a>Disponibilidade

- Estado de libertação: **Disponibilidade geral**
- Funções necessárias: **Leitor de segurança** e [função de leitor de registo de contentores Azure](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Nuvens: 
    - nuvens comerciais ✔
    - ✘ nuvem do governo dos EUA
    - ✘ nuvem do Governo da China, outras nuvens gov


## <a name="when-are-images-scanned"></a>Quando as imagens são digitalizadas?

Sempre que uma imagem é empurrada para o seu registo, o Centro de Segurança digitaliza automaticamente essa imagem. Para ativar a digitalização de uma imagem, empurre-a para o seu repositório.

Quando a varredura termina (normalmente após aproximadamente 10 minutos, mas pode ser até 40 minutos), os resultados estão disponíveis como recomendações do Centro de Segurança como esta:

[![Amostra da recomendação do Centro de Segurança Azure sobre vulnerabilidades descobertas numa imagem hospedada do Registo de Contentores Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>Benefícios da integração

O Security Center identifica registos ACR baseados na ARM na sua subscrição e fornece perfeitamente:

* **Vulnerabilidade nativa do Azure procurando** todas as imagens de Linux empurradas. O Security Center verifica a imagem usando um scanner do fornecedor líder de pesquisa de vulnerabilidades da indústria, Qualys. Esta solução nativa é perfeitamente integrada por padrão.

* **Recomendações de segurança** para imagens Linux com vulnerabilidades conhecidas. O Centro de Segurança fornece detalhes de cada vulnerabilidade reportada e uma classificação de gravidade. Além disso, dá orientação para como remediar as vulnerabilidades específicas encontradas em cada imagem empurrada para o registo.

![Centro de Segurança Azure e Registo de Contentores Azure (ACR) visão geral de alto nível](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="acr-with-security-center-faq"></a>ACR com Centro de Segurança FAQ

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Que tipo de imagens o Azure Security Center pode digitalizar?
O Centro de Segurança analisa imagens baseadas em Linux OS que fornecem acesso à concha. 

O scanner Qualys não suporta imagens super minimalistas, como imagens [de rascunho de Docker,](https://hub.docker.com/_/scratch/) ou imagens "Distroless" que contêm apenas a sua aplicação e as suas dependências de tempo de execução sem um gestor de pacotes, concha ou SISTEMA.

### <a name="how-does-azure-security-center-scan-an-image"></a>Como é que o Azure Security Center digitaliza uma imagem?
A imagem é retirada do registo. É então executado numa caixa de areia isolada com o scanner Qualys que extrai uma lista de vulnerabilidades conhecidas.

O Centro de Segurança filtra e classifica as descobertas do scanner. Quando uma imagem é saudável, o Centro de Segurança marca-a como tal. O Centro de Segurança gera recomendações de segurança apenas para imagens que têm problemas a resolver. Ao notificar apenas quando há problemas, o Centro de Segurança reduz o potencial de alertas informativos indesejados.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Com que frequência o Azure Security Center digitaliza as minhas imagens?
As imagens são ativadas em cada impulso.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da digitalização através da REST API?
Sim. Os resultados estão em [Sub-Avaliações Rest API](/rest/api/securitycenter/subassessments/list/). Além disso, pode utilizar o Azure Resource Graph (ARG), a API semelhante a Kusto para todos os seus recursos: uma consulta pode obter uma digitalização específica.
 



## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Proteção de Máquinas Virtuais](security-center-virtual-machine-protection.md) - Descreve as recomendações do Centro de Segurança
