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
ms.date: 08/02/2020
ms.author: memildin
ms.openlocfilehash: b66969b26a801e6bd9aacf999c1c1ef9179ef1bd
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534673"
---
# <a name="azure-container-registry-image-scanning-by-security-center"></a>Digitalização da imagem do registo do contentor Azure pelo Centro de Segurança

O Registo de Contentores Azure (ACR) é um serviço de registo privado e gerido do Docker que armazena e gere as suas imagens de contentores para implantações do Azure num registo central. Baseia-se no registo 2.0 do Docker.

Se estiver no nível padrão do Centro de Segurança Azure, pode adicionar o pacote de registos de contentores. Esta funcionalidade opcional traz uma visibilidade mais profunda às vulnerabilidades das imagens nos registos baseados no Azure Resource Manager. Ative ou desative o pacote ao nível de subscrição para cobrir todos os registos numa subscrição. Esta funcionalidade é carregada por imagem, como mostra a [página de preços](security-center-pricing.md). Ativando o pacote de registos de contentores, garante que o Centro de Segurança está pronto para digitalizar imagens que são empurradas para o registo. 

## <a name="availability"></a>Disponibilidade

- Estado de libertação: **Disponibilidade geral**
- Funções necessárias: **Leitor de segurança** e [função de leitor de registo de contentores Azure](https://docs.microsoft.com/azure/container-registry/container-registry-roles)
- Registos e imagens suportados:
    - ✔ registos ACR hospedados em Linux que são acessíveis a partir da internet pública e fornecem acesso à concha.
    - ✘ registos ACR hospedados no Windows.
    - ✘ registos 'Privados' - O Centro de Segurança exige que os seus registos sejam acessíveis a partir da internet pública. O Security Center não pode atualmente ligar-se ou digitalizar registos com acesso limitado com uma firewall, um ponto final de serviço ou pontos finais privados, como o Azure Private Link.
    - ✘ imagens super minimalistas, como imagens [de rascunho de Docker,](https://hub.docker.com/_/scratch/) ou imagens "descaídas" que contêm apenas uma aplicação e as suas dependências de tempo de execução sem um gestor de pacotes, concha ou SISTEMA.
- Nuvens: 
    - nuvens comerciais ✔
    - ✘ nuvem do governo dos EUA
    - ✘ nuvem do Governo da China, outras nuvens do governo


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
A [secção de disponibilidade](#availability) lista os tipos de registos de contentores suportados pelo pacote de registos de contentores. 

Se os registos que não forem suportados estiverem ligados à sua subscrição do Azure, não serão digitalizados e não serão cobrados por eles.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* [Proteção de Máquinas Virtuais](security-center-virtual-machine-protection.md) - Descreve as recomendações do Centro de Segurança
