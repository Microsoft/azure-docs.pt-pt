---
title: Imagens de Red Hat Enterprise Linux no Azure | Documentos da Microsoft
description: Saiba mais sobre imagens de Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/18/2019
ms.author: borisb
ms.openlocfilehash: d42349de4324428103ccca3ef270ceb9109ca0c7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011661"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Imagens de Red Hat Enterprise Linux no Azure
Este artigo descreve as imagens de Red Hat Enterprise Linux (RHEL) disponíveis no Azure Marketplace, juntamente com as políticas em torno de seus nomenclatura e retenção.

Podem encontrar informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL no [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.

>[!Important]
> Imagens RHEL atualmente disponíveis no Azure marketplace suportam traga-Your-Own-subscrição (BYOS) ou pay as you go (PAYG) a modelos de licenciamento. O [Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md) e mudança dinâmica entre BYOS e PAYG não é suportada. Modo de licenciamento de comutação requer a reimplementação da VM a partir da imagem correspondente.

>[!Note]
> Para qualquer problema relacionado com RHEL imagens na galeria do marketplace do Azure, submeta um pedido de suporte com a Microsoft.

## <a name="images-available-in-the-ui"></a>Imagens disponíveis na interface de Usuário
Ao procurar "Red Hat" no Marketplace ou quando cria um recurso no portal do Azure da interface do Usuário, verá um subconjunto das imagens disponíveis do RHEL e produtos relacionados do Red Hat. Sempre pode obter o conjunto completo de imagens VM disponíveis com a CLI/PowerShell/API do Azure.

Para ver o conjunto completo de imagens disponíveis do Red Hat no Azure, execute o seguinte comando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Convenção de nomenclatura
Imagens de VM no Azure são organizadas por publicador, oferta, SKU e versão. A combinação do publicador: oferta: SKU:Version é a imagem URN e identifica exclusivamente a imagem a ser utilizada.

Por exemplo, `RedHat:RHEL:7-RAW:7.6.2018103108` refere-se a uma imagem de não processados particionada RHEL 7,6 criada no dia 31 de Outubro de 2018.

Um exemplo de como criar uma VM do RHEL 7,6 é mostrado abaixo.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>O moniker "mais recente"
A API REST do Azure permite a utilização do moniker "mais recente" para a versão em vez da versão específica. Utilizar "mais recente" irá aprovisionar a imagem mais recente disponível para o determinado publicador, oferta e SKU.

Por exemplo, `RedHat:RHEL:7-RAW:latest` refere-se para a imagem mais recente RHEL 7 família brutos particionada disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar a versão mais recente segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Convenção de nomenclatura atual
Todas as imagens RHEL atualmente publicadas utilizam o modelo de pay as you go e estão ligadas ao [Red Hat atualização de infra-estrutura (RHUI) no Azure](https://aka.ms/rhui-update). Devido a uma limitação de design do RHUI, uma nova Convenção de nomenclatura foi adotada para imagens RHEL 7 de famílias. A nomenclatura do família de RHEL 6 não foram alterada neste momento.

A limitação é o fato de que quando um não-seletiva `yum update` é executada numa VM ligado ao RHUI, a versão RHEL for atualizada para o versão mais recente da família atual. Para obter mais informações, consulte [esta ligação](https://aka.ms/rhui-udate). Isso pode resultar em confusão quando uma imagem RHEL 7.2 aprovisionada se torna a 7,6 RHEL após uma atualização. Ainda pode aprovisionar a partir de uma imagem mais antiga, conforme ilustrado nos exemplos acima, especificando a versão necessária explicitamente. Se a versão necessária não for especificada durante o aprovisionamento de uma nova imagem RHEL 7, em seguida, a imagem mais recente será aprovisionada.

>[!NOTE]
> No RHEL para o conjunto SAP de imagens, a versão RHEL permanece fixa. Assim, sua Convenção de nomenclatura inclui uma versão específica no SKU.

>[!NOTE]
> RHEL 6 conjunto de imagens não foram movidas para a nova Convenção de nomenclatura.

As seguintes ofertas são que SKUs estão atualmente disponíveis para utilização geral:

Oferta| SKU | Criação de partições | Aprovisionamento | Notas
:----|:----|:-------------|:-------------|:-----
RHEL | 7-RAW | RAW | Agente Linux | Família do RHEL 7 de imagens
| | 7-LVM | LVM | Agente Linux | Família do RHEL 7 de imagens
| | 7-RAW-CI | RAW-CI | Inicialização da cloud | Família do RHEL 7 de imagens
| | 6.7 | RAW | Agente Linux | Imagens RHEL 6.7, convenção de nomenclatura antiga
| | 6.8 | RAW | Agente Linux | Mesmo como anteriormente para RHEL 6.8
| | 6.9 | RAW | Agente Linux | Mesmo como anteriormente para RHEL 6.9
| | 6.10 | RAW | Agente Linux | Mesmo como anteriormente para RHEL 6.10
| | 7.2 | RAW | Agente Linux | Mesmo como anteriormente para RHEL 7.2
| | 7.3 | RAW | Agente Linux | Mesmo como anteriormente para RHEL 7.3
| | 7.4 | RAW | Agente Linux | Mesmo como anteriormente para RHEL 7.4
| | 7.5 | RAW | Agente Linux | Mesmo como anteriormente para RHEL 7.5
RHEL SAP | 7.4 | LVM | Agente Linux | RHEL 7.4 para SAP HANA e aplicações empresariais
| | 7.5 | LVM | Agente Linux | RHEL 7.5 para o SAP HANA e aplicações empresariais
RHEL-SAP-HANA | 6.7 | RAW | Agente Linux | RHEL 6.7 para o SAP HANA
| | 7.2 | LVM | Agente Linux | 7.2 de RHEL for SAP HANA
| | 7.3 | LVM | Agente Linux | 7.3 de RHEL for SAP HANA
APLICAÇÕES DE SAP RHEL | 6.8 | RAW | Agente Linux | 6.8 de RHEL for SAP Business Applications
| | 7.3 | LVM | Agente Linux | 7.3 de RHEL for SAP Business Applications

### <a name="old-naming-convention"></a>Convenção de nomenclatura antiga
A família de RHEL 7 de imagens e a família de imagens do RHEL 6 utilizado versões específicas em suas SKUs até que a alteração de convenção de nomenclatura explicado acima.

Encontrará SKUs numérico na lista de imagens completas. Microsoft e a Red Hat irão criar novos SKUs numérico quando sai de uma nova versão secundária.

### <a name="other-available-offers-and-skus"></a>Outras ofertas disponíveis e SKUs
A lista completa de ofertas disponíveis e SKUs que pode incluir imagens adicionais além do que está listado na tabela acima, por exemplo, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Estas ofertas podem ser utilizadas para fornecer suporte de soluções do marketplace específico, ou poderia ser publicados para fins de testes e de pré-visualizações. Eles podem ser alterados ou removidos em qualquer altura, sem aviso. Não usá-los, a menos que a sua presença foram documentada publicamente pela Microsoft ou Red Hat.

## <a name="publishing-policy"></a>Política de publicação
Microsoft e a Red Hat atualizar imagens à medida que novas versões secundárias são lançados, conforme necessário para resolver específicas relacionadas ou para as alterações/atualizações de configuração ocasionais. Esforçamo-nos fornecer imagens atualizadas assim que possível – dentro de três dias úteis a seguir uma versão ou a disponibilidade de uma correção CVE.

Iremos atualizar apenas a versão secundária atual numa família de determinada imagem. Com o lançamento de uma versão secundária mais recente, podemos param de atualizar a versão secundária mais antiga. Por exemplo, com o lançamento do RHEL 7,6, imagens RHEL 7.5 já não vai ser atualizada.

>[!NOTE]
> VMs do Azure Active Directory, aprovisionada a partir da RHEL pay as you go imagens estão ligadas a RHUI o Azure e podem receber atualizações e correções assim que são lançadas pela Red Hat e replicados para o Azure RHUI (normalmente em menos de 24 horas após o lançamento oficial da Red Hat) . Estas VMs não necessitam de uma nova imagem publicada para obter as atualizações e os clientes têm controle total sobre o quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem
A nossa política atual é manter todas as imagens publicadas anteriormente. Reservamo-no direito de remover as imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, as imagens com configurações incorretas devido à plataforma subsequente ou atualizações de componentes podem ser removidas. Imagens que podem ser removidas seguirão a diretiva atual de mercado para fornecem notificações até 30 dias antes da remoção de imagem.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a infraestrutura de atualização do Azure Red Hat [aqui](https://aka.ms/rhui-update).
* Podem encontrar informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL no [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.