---
title: Azure Defender para registos de contentores - os benefícios e características
description: Conheça os benefícios e funcionalidades do Azure Defender para registos de contentores.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ad880b7c23c687530a79ca1123474e94c923e150
ms.sourcegitcommit: b849ecdc8aa97337299b0f09970b7810c59cd044
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96310396"
---
# <a name="introduction-to-azure-defender-for-container-registries"></a>Introdução ao Azure Defender dos registos de contentores

O Registo de Contentores Azure (ACR) é um serviço de registo privado e gerido do Docker que armazena e gere as suas imagens de contentores para implantações do Azure num registo central. Baseia-se no registo 2.0 do Docker.

Para proteger todos os registos baseados no Azure Resource Manager na sua subscrição, ative **o Azure Defender para registos** de contentores ao nível da subscrição. O Centro de Segurança irá então digitalizar imagens que são empurradas para o registo, importadas para o registo, ou quaisquer imagens retiradas nos últimos 30 dias. Esta funcionalidade é carregada por imagem.

[!INCLUDE [Defender for container registries availability info](../../includes/security-center-availability-defender-for-container-registries.md)]

## <a name="what-are-the-benefits-of-azure-defender-for-container-registries"></a>Quais são os benefícios do Azure Defender para os registos de contentores?

O Security Center identifica os registos ACR baseados em Azure Resource Manager na sua subscrição e fornece perfeitamente a avaliação e gestão de vulnerabilidade nativa do Azure para as imagens do seu registo.

**O Azure Defender para registos de contentores** inclui um scanner de vulnerabilidade para digitalizar as imagens nos registos do Registo de Contentores Azure baseados em Recursos Azure e fornecer uma visibilidade mais profunda sobre as vulnerabilidades das suas imagens. O scanner integrado é alimentado pela Qualys, o fornecedor líder de pesquisa de vulnerabilidades da indústria.

Quando os problemas forem encontrados – pelo Qualys ou pelo Security Center – será notificado no painel do Centro de Segurança. Para cada vulnerabilidade, o Security Center fornece recomendações acccáveis, juntamente com uma classificação de gravidade, e orientações para como remediar o problema. Para obter mais informações sobre as recomendações do Centro de Segurança para os contentores, consulte a [lista de referência das recomendações.](recommendations-reference.md#recs-containers)

O Centro de Segurança filtra e classifica as descobertas do scanner. Quando uma imagem é saudável, o Centro de Segurança marca-a como tal. O Centro de Segurança gera recomendações de segurança apenas para imagens que têm problemas a resolver. O Centro de Segurança fornece detalhes de cada vulnerabilidade reportada e uma classificação de gravidade. Além disso, dá orientação para como remediar as vulnerabilidades específicas encontradas em cada imagem.

Ao notificar apenas quando há problemas, o Centro de Segurança reduz o potencial de alertas informativos indesejados.


> [!TIP]
> Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:
>
> - [Centro de Segurança Azure e segurança de contentores](container-security.md)
> - [Introdução ao Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="when-are-images-scanned"></a>Quando as imagens são digitalizadas?

Há três gatilhos para uma verificação de imagem:

- **Em push** - Sempre que uma imagem é empurrada para o seu registo, o Centro de Segurança digitaliza automaticamente essa imagem. Para ativar a digitalização de uma imagem, empurre-a para o seu repositório.

- **Recentemente puxado** - Uma vez que são descobertas novas vulnerabilidades todos os dias, **o Azure Defender para registos de contentores** também verifica qualquer imagem que tenha sido puxada nos últimos 30 dias. Não há nenhuma taxa adicional para um rescan; como mencionado acima, você é cobrado uma vez por imagem.

- **Sobre a importação** - O Registo de Contentores Azure tem ferramentas de importação para levar imagens ao seu registo a partir de Docker Hub, Microsoft Container Registry ou outro registo de contentores Azure. **O Azure Defender para registos de contentores** verifica quaisquer imagens suportadas que importe. Saiba mais em [Importar imagens de contentores para um registo de contentores.](../container-registry/container-registry-import-images.md)
 
A tomografia completa normalmente em 2 minutos, mas pode demorar até 15 minutos. As descobertas são disponibilizadas como recomendações do Centro de Segurança, como esta:

[![Amostra da recomendação do Centro de Segurança Azure sobre vulnerabilidades descobertas numa imagem hospedada do Registo de Contentores Azure (ACR)](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)


## <a name="how-does-security-center-work-with-azure-container-registry"></a>Como funciona o Centro de Segurança com o Registo de Contentores Azure

Abaixo está um diagrama de alto nível dos componentes e benefícios de proteger os seus registos com o Centro de Segurança.

![Centro de Segurança Azure e Registo de Contentores Azure (ACR) visão geral de alto nível](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)




## <a name="faq-for-azure-container-registry-image-scanning"></a>FAQ para digitalização da imagem do registo do contentor de Azure

### <a name="how-does-security-center-scan-an-image"></a>Como é que o Centro de Segurança verifica uma imagem?
A imagem é retirada do registo. É então executado numa caixa de areia isolada com o scanner Qualys que extrai uma lista de vulnerabilidades conhecidas.

O Centro de Segurança filtra e classifica as descobertas do scanner. Quando uma imagem é saudável, o Centro de Segurança marca-a como tal. O Centro de Segurança gera recomendações de segurança apenas para imagens que têm problemas a resolver. Ao notificar apenas quando há problemas, o Centro de Segurança reduz o potencial de alertas informativos indesejados.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da digitalização através da REST API?
Sim. Os resultados encontram-se em [API REST das Subavaliações](/rest/api/securitycenter/subassessments/list/). Além disso, pode utilizar o Azure Resource Graph (ARG), a API semelhante a Kusto para todos os seus recursos: uma consulta pode obter uma digitalização específica.

### <a name="what-registry-types-are-scanned-what-types-are-billed"></a>Que tipos de registo são digitalizados? Que tipos são cobrados?
Para obter uma lista dos tipos de registos de contentores suportados pelo Azure Defender para registos de contentores, consulte [Disponibilidade](defender-for-container-registries-usage.md#availability).

Se ligar registos não suportados à sua assinatura Azure, eles não serão digitalizados e não será cobrado por eles.

### <a name="can-i-customize-the-findings-from-the-vulnerability-scanner"></a>Posso personalizar as descobertas do scanner de vulnerabilidade?
Sim. Se tiver uma necessidade organizacional de ignorar uma descoberta, em vez de remediar, pode desativá-la opcionalmente. As descobertas desativadas não afetam a sua pontuação segura ou geram ruídos indesejados.

[Saiba como criar regras para desativar as descobertas da ferramenta integrada de avaliação de vulnerabilidades.](defender-for-container-registries-usage.md#disable-specific-findings-preview)

### <a name="why-is-security-center-alerting-me-to-vulnerabilities-about-an-image-that-isnt-in-my-registry"></a>Porque é que o Centro de Segurança está a alertar-me para as vulnerabilidades sobre uma imagem que não está no meu registo?
O Centro de Segurança fornece avaliações de vulnerabilidade para cada imagem empurrada ou puxada num registo. Algumas imagens podem reutilizar tags a partir de uma imagem que já foi digitalizada. Por exemplo, pode reatribuir a etiqueta "Mais recente" sempre que adicionar uma imagem a uma digestão. Nesses casos, a imagem 'antiga' ainda existe no registo e ainda pode ser puxada pela sua digestão. Se a imagem tiver descobertas de segurança e for retirada, exporá vulnerabilidades de segurança.


## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Digitalize as suas imagens para obter vulnerabilidades](defender-for-container-registries-usage.md)