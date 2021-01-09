---
title: Azure Red Hat OpenShift suporta ciclo de vida
description: Compreenda o ciclo de vida de suporte e versões suportadas para Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 5b5ae695ac11ae687a9b076005a765feaa46f83c
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98049512"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Ciclo de vida do suporte do Azure Red Hat OpenShift 4

A Red Hat lança versões menores da Plataforma de Contentores OpenShift (OCP) de chapéu vermelho aproximadamente de três em três meses. Estes lançamentos incluem novas funcionalidades e melhorias. As versões de patch são mais frequentes (normalmente semanais) e destinam-se apenas a correções críticas de bugs dentro de uma versão menor. Estes lançamentos de patch podem incluir correções para vulnerabilidades de segurança ou bugs importantes.

O Azure Red Hat OpenShift é construído a partir de lançamentos específicos de OCP. Este artigo abrange as versões de OCP que são suportadas para Azure Red Hat OpenShift e detalhes sobre upgrades, depreciações e política de apoio.

## <a name="red-hat-openshift-versions"></a>Versões Red Hat OpenShift

A Plataforma de Recipientes OpenShift do Chapéu Vermelho utiliza a versão semântica. A versão semântica utiliza diferentes níveis de números de versão para especificar diferentes níveis de versão. A tabela a seguir ilustra as diferentes partes de um número de versão semântica, neste caso utilizando o número da versão de exemplo 4.4.11.

|Versão principal (x)|Versão menor (y)|Patch (z)|
|-|-|-|
|4|4|11|

Cada número na versão indica compatibilidade geral com a versão anterior:

* **Versão principal**: Não estão previstas grandes versões neste momento. As versões principais mudam quando a API incompatível muda ou a retrocompatibilidade pode ser quebrada.
* **Versão menor**: Lançado aproximadamente a cada três meses. As atualizações de versão menores podem incluir adições de recursos, melhorias, depreciações, remoção, correções de bugs, melhorias de segurança e outras melhorias.
* **Patches**: Normalmente libertados todas as semanas, ou conforme necessário. As atualizações da versão patch podem incluir correções de bugs, melhorias de segurança e outras melhorias.

Os clientes devem ter como objetivo executar o mais recente lançamento menor da versão principal que estão a executar. Por exemplo, se o seu cluster de produção estiver em 4.4, e 4.5 é a versão menor geralmente disponível para a série 4, deve atualizar para 4.5 o mais rápido possível.

### <a name="upgrade-channels"></a>Canais de upgrade

Os canais de atualização estão ligados a uma versão menor da Plataforma de Contentores OpenShift (OCP) do chapéu vermelho. Por exemplo, os canais de atualização OCP 4.4 nunca incluirão um upgrade para uma versão 4.5. Os canais de atualização controlam apenas a seleção de lançamento e não impactam a versão do cluster.

O Azure Red Hat OpenShift 4 suporta apenas canais estáveis. Por exemplo: estável-4.4.

Pode utilizar o canal estável 4.5 para atualizar a partir de uma versão menor anterior do Azure Red Hat OpenShift. Os clusters atualizados utilizando canais rápidos, pré-lançados e candidatos não serão suportados.

Se mudar para um canal que não inclua a sua versão atual, pode recomendar-se um alerta e não é possível recomendar atualizações, mas pode voltar a mudar com segurança para o seu canal original em qualquer ponto.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Política de suporte da plataforma de recipientes OpenShift red Hat OpenShift

O Azure Red Hat OpenShift suporta duas versões menores (GA) geralmente disponíveis (GA) da Plataforma de Contentores OpenShift do Chapéu Vermelho:
* A mais recente versão ga menor que é lançada em Azure Red Hat OpenShift (a que nos referiremos como N)
* Uma versão menor anterior (N-1)

Se disponível num canal de upgrade estável, as versões menores mais recentes (N+1, N+2) disponíveis em OCP a montante também podem ser suportadas.

As atualizações críticas de patch são aplicadas automaticamente aos clusters pelos Engenheiros de Fiabilidade do Site OpenShift do Chapéu Vermelho Azure (SRE). Os clientes que desejem instalar atualizações de patch com antecedência são livres de o fazer.

Por exemplo, se o Azure Red Hat OpenShift introduzir 4.5.z hoje, o suporte é fornecido para as seguintes versões:

|Nova versão menor|Lista de versão suportada|
|-|-|
|4.5.z|4.5.z, 4.4.z|

".z" é representativo das versões de patch. Se estiver disponível num canal de upgrade estável, os clientes também podem fazer upgrade para 4.6.z.

Quando uma nova versão menor é introduzida, a versão menor mais antiga é depreciada e removida. Por exemplo, digamos que a lista de versão suportada atual é de 4.5.z e 4.4.z. Quando o Azure Red Hat OpenShift lançar 4.6.z, o lançamento de 4.4.z será removido e ficará sem suporte dentro de 30 dias.

> [!NOTE]
> Por favor, note que se os clientes estiverem a executar uma versão openShift de chapéu vermelho não suportada, podem ser solicitados a fazer upgrade quando solicitarem suporte para o cluster. Os clusters que executam lançamentos abertos de chapéu vermelho não suportados não são cobertos pelo Azure Red Hat OpenShift SLA.

## <a name="release-and-deprecation-process"></a>Processo de libertação e depreciação

Pode fazer referência a lançamentos e depreciações da próxima versão no Calendário de Lançamento aberto do chapéu vermelho Azure.

Para novas versões menores da Plataforma de Contentores OpenShift do Chapéu Vermelho:
* A equipa Azure Red Hat OpenShift SRE publica um pré-anúncio com a data prevista de um novo lançamento de versão e respetiva depreciação da versão antiga no [Azure Red Hat OpenShift Release, que regista](https://github.com/Azure/OpenShift/releases) pelo menos 30 dias antes da remoção.
* A equipa Azure Red Hat OpenShift SRE publica uma notificação de saúde de serviço disponível para todos os clientes com acesso a Azure Red Hat OpenShift e portal, e envia um e-mail aos administradores de subscrição com as datas de remoção da versão planeadas.
* Os clientes têm 30 dias desde a remoção da versão para atualizar para uma versão menor suportada para continuar a receber suporte.

Para novas versões de patch da Plataforma de Contentores OpenShift do Chapéu Vermelho:
* Devido à natureza urgente das versões patch, estas podem ser introduzidas no serviço pela equipa Azure Red Hat OpenShift SRE à medida que ficam disponíveis.
* Em geral, a equipa Azure Red Hat OpenShift SRE não realiza comunicações amplas para a instalação de novas versões de patch. No entanto, a equipa monitoriza e valida constantemente os patches CVE disponíveis para os suportar em tempo útil. Se for necessária a ação do cliente, a equipa notificará os clientes sobre a atualização.

## <a name="supported-versions-policy-exceptions"></a>Exceções políticas de versões apoiadas

A equipa Azure Red Hat OpenShift SRE reserva-se o direito de adicionar ou remover versões novas/existentes, ou atrasar as próximas versões de lançamento menores, que foram identificadas como com um ou mais problemas críticos de produção com impacto em bugs ou problemas de segurança sem aviso prévio.

As versões específicas do patch podem ser ignoradas ou o lançamento pode ser acelerado dependendo da gravidade do bug ou problema de segurança.

## <a name="azure-portal-and-cli-versions"></a>Versões do portal Azure e CLI

Quando implementa um cluster Azure Red Hat OpenShift no portal ou com o Azure CLI, o cluster é padrão para a versão mais recente (N) menor e o mais recente patch crítico. Por exemplo, se o Azure Red Hat OpenShift suportar 4.5.z e 4.4.z, a versão padrão para novas instalações é de 4.5.z. Os clientes que desejem utilizar a versão menor oparante (N+1, N+2) podem atualizar o seu cluster a qualquer momento para qualquer versão disponível nos canais de upgrade estáveis.

## <a name="azure-red-hat-openshift-release-calendar"></a>Calendário de lançamento do Azure Red Hat OpenShift

Consulte o seguinte guia para o histórico de lançamento da [plataforma de desbloqueio Red Hat OpenShift (a montante).](https://access.redhat.com/support/policy/updates/openshift/#dates)

|Versão OCP|Liberação a montante|Disponibilidade geral aberta do chapéu vermelho azul|Fim da Vida|
|-|-|-|-|
|4.3|Janeiro de 2020|Abril de 2020| Agosto de 2020|
|4.4|Maio de 2020|Julho de 2020|4.6 GA|
|4,5|Julho de 2020| Novembro de 2020|4.7 GA
|4,6|Outubro de 2020| fevereiro de 2021|4.8 GA|

## <a name="faq"></a>FAQ

**O que acontece quando um utilizador atualiza um cluster OpenShift com uma versão menor que não é suportada?**

Se estiver na versão N-2 ou mais antiga, significa que está fora do suporte e será solicitado para fazer upgrade. Quando a sua atualização da versão N-2 para N-1 tiver sucesso, está de volta às nossas políticas de apoio. Por exemplo:
* Se a versão Azure Red Hat OpenShift mais antiga for de 4.4.z e estiver em 4.3.z ou mais antiga, está fora do suporte.
* Quando a atualização de 4.3.z para 4.4.z ou superior tiver sucesso, está de volta às nossas políticas de apoio.

Reverter o seu cluster para uma versão anterior, ou um revés, não é suportado. Apenas o upgrade para uma versão mais recente é suportado.

**O que significa "Fora do Apoio"?**

"Fora do Suporte" significa que a versão que está a executar está fora da lista de versões suportadas, e pode ser-lhe pedido que atualize o cluster para uma versão suportada ao solicitar suporte, a menos que esteja dentro do período de carência de 30 dias após a depreciação da versão. Além disso, o Azure Red Hat OpenShift não faz qualquer tempo de funcionamento ou garantias de SLA para clusters fora da lista de versões suportadas no final do período de graça de 30 dias.
