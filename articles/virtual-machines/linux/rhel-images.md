---
title: Red Hat Enterprise Linux imagens no Azure | Microsoft Docs
description: Saiba mais sobre as imagens de Red Hat Enterprise Linux no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 7d34e480dd3cf90f1948e83ea1d18c04f1dcdce2
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854430"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Red Hat Enterprise Linux imagens no Azure
Este artigo descreve as imagens disponíveis do Red Hat Enterprise Linux (RHEL) no Azure Marketplace, juntamente com as políticas relacionadas à sua nomenclatura e retenção.

Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

>[!Important]
> As imagens RHEL atualmente disponíveis no Azure Marketplace dão suporte a modelos de licenciamento BYOS (traga sua própria assinatura) ou PAYG (pago conforme o uso). Não há suporte para o [benefício de uso híbrido do Azure](../windows/hybrid-use-benefit-licensing.md) e para a alternância dinâmica entre BYOS e PAYG. Alternar o modo de licenciamento requer a reimplantação da VM da imagem correspondente.

>[!Note]
> Para qualquer problema relacionado a imagens RHEL na galeria do Azure Marketplace, registre um tíquete de suporte com a Microsoft.

## <a name="images-available-in-the-ui"></a>Imagens disponíveis na interface do usuário
Ao Pesquisar "Red Hat" no Marketplace ou ao criar um recurso na interface do usuário portal do Azure, você verá um subconjunto de imagens RHEL disponíveis e produtos Red Hat relacionados. Você sempre pode obter o conjunto completo de imagens de VM disponíveis usando o CLI do Azure/PowerShell/API.

Para ver o conjunto completo de imagens do Red Hat disponíveis no Azure, execute o seguinte comando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>Convenção de nomenclatura
As imagens de VM no Azure são organizadas por editor, oferta, SKU e versão. A combinação de Publisher: oferta: SKU: Version é a imagem URN e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:7-RAW:7.6.2018103108` refere-se a uma imagem de partição bruta do RHEL 7,6 criada em 31 de outubro de 2018.

Uma amostra de como criar uma VM RHEL 7,6 é mostrada abaixo.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>O moniker "mais recente"
A API REST do Azure permite o uso do moniker "mais recente" para a versão em vez da versão específica. Usar "mais recente" provisionará a imagem mais recente disponível para o Publicador, a oferta e a SKU fornecidos.

Por exemplo, `RedHat:RHEL:7-RAW:latest` refere-se à última imagem de partição bruta do RHEL 7 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar a mais recente segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).

### <a name="current-naming-convention"></a>Convenção de nomenclatura atual
Todas as imagens RHEL atualmente publicadas usam o modelo pago conforme o uso e estão conectadas à [RHUI (infraestrutura de atualização do Red Hat) no Azure](https://aka.ms/rhui-update). Uma nova Convenção de nomenclatura foi adotada para imagens da família RHEL 7 nas quais o esquema de particionamento de disco (RAW, LVM) é especificado na SKU em vez da versão. A versão da imagem RHEL conterá 7-RAW ou 7-LVM. A nomenclatura da família RHEL 6 não foi alterada no momento.

Haverá 2 tipos de SKUs de imagem do RHEL 7 nesta Convenção de nomenclatura: SKUs que listam a versão secundária e SKUs que não têm. Se você quiser usar uma SKU 7-RAW ou 7-LVM, poderá especificar a versão secundária do RHEL que deseja implantar na versão. Se você escolher a versão "mais recente", será provisionada a versão secundária mais recente do RHEL.

>[!NOTE]
> No RHEL for SAP conjunto de imagens, a versão do RHEL permanece fixa. Dessa forma, sua Convenção de nomenclatura inclui uma versão específica na SKU.

>[!NOTE]
> O conjunto de imagens RHEL 6 não foi movido para a nova Convenção de nomenclatura.

## <a name="extended-update-support-eus"></a>Suporte de atualização estendida (EUS)
A partir de abril de 2019, as imagens RHEL estão disponíveis que são anexadas aos repositórios EUS (suporte de atualização estendida) por padrão. Mais detalhes sobre o RHEL EUS estão disponíveis na [documentação do Red Hat](https://access.redhat.com/articles/rhel-eus).

Instruções sobre como mudar sua VM para EUS e mais detalhes sobre as datas de fim de suporte do EUS estão disponíveis [aqui](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> Não há suporte para EUS em extras de RHEL. Isso significa que, se você estiver instalando um pacote que geralmente está disponível no canal de extras do RHEL, não poderá fazer isso enquanto estiver em EUS. O ciclo de vida do produto Red Hat extras é detalhado [aqui](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Para clientes que desejam usar imagens EUS:
Os clientes que desejam usar imagens anexadas a repositórios EUS devem usar a imagem RHEL que contém um número de versão secundária do RHEL no SKU. Essas imagens serão particionadas de forma bruta (ou seja, não LVM).

Por exemplo, você pode ver as seguintes imagens do RHEL 7,4 disponíveis:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
Nesse caso, `RedHat:RHEL:7.4:7.4.2019041718` será anexado a repositórios do eus por padrão e `RedHat:RHEL:7-RAW:7.4.2018010506` será anexado a repositórios não eus por padrão.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Para clientes que não desejam usar imagens EUS:
Se você não quiser usar uma imagem conectada ao EUS por padrão, implante usando uma imagem que não contenha um número de versão secundário na SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS
A tabela a seguir será aplicada a imagens RHEL que contêm uma versão secundária na SKU.

>[!NOTE]
> No momento da gravação, apenas RHEL 7,4 e versões secundárias posteriores têm suporte EUS. EUS não tem mais suporte para RHEL < = 7,3.

Versão secundária |Exemplo de imagem EUS              |Status do EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7,4      |RedHat:RHEL:7.4:7.4.2019041718 | As imagens publicadas em abril de 2019 e posteriores serão EUS por padrão|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | As imagens publicadas em junho de 2019 e posteriores serão EUS por padrão |
RHEL 7,6      |RedHat:RHEL:7.6:7.6.2019052206 | As imagens publicadas podem ser 2019 e posteriores serão EUS por padrão  |
RHEL 8,0      |N/A                            | Não há imagens atualmente disponíveis no EUS                 |


## <a name="list-of-rhel-images-available"></a>Lista de imagens RHEL disponíveis
As ofertas a seguir são SKUs disponíveis no momento para uso geral:

Oferta| SKU | Criação de partições | Aprovisionamento | Notas
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Agente Linux | Família RHEL 7 de imagens. <br> Não anexado a repositórios do EUS por padrão.
|             | 7-LVM    | LVM    | Agente Linux | Família RHEL 7 de imagens. <br> Não anexado a repositórios do EUS por padrão.
|             | 7-RAW-CI | RAW-CI | Inicialização da cloud  | Família RHEL 7 de imagens. <br> Não anexado a repositórios do EUS por padrão.
|             | 6.7      | RAW    | Agente Linux | Imagens RHEL 6,7, Convenção de nomenclatura antiga
|             | 6.8      | RAW    | Agente Linux | O mesmo que o RHEL 6,8
|             | 6.9      | RAW    | Agente Linux | O mesmo que o RHEL 6,9
|             | 6.10     | RAW    | Agente Linux | O mesmo que o RHEL 6,10
|             | 7.2      | RAW    | Agente Linux | O mesmo que o RHEL 7,2
|             | 7.3      | RAW    | Agente Linux | O mesmo que o RHEL 7,3
|             | 7.4      | RAW    | Agente Linux | O mesmo que o RHEL 7,4. <br> Anexado a repositórios do EUS por padrão a partir de abril de 2019
|             | 7.5      | RAW    | Agente Linux | O mesmo que o RHEL 7,5. <br> Anexado a repositórios do EUS por padrão a partir de junho de 2019
|             | 7,6      | RAW    | Agente Linux | O mesmo que o RHEL 7,6. <br> Anexado a repositórios do EUS por padrão a partir de maio de 2019
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7,4 para aplicativos de SAP HANA e de negócios
|             | 7.5      | LVM    | Agente Linux | RHEL 7,5 para aplicativos de SAP HANA e de negócios
RHEL-SAP-HANA | 6.7      | RAW    | Agente Linux | RHEL 6,7 para SAP HANA
|             | 7.2      | LVM    | Agente Linux | RHEL 7,2 para SAP HANA
|             | 7.3      | LVM    | Agente Linux | RHEL 7,3 para SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Agente Linux | RHEL 6,8 para SAP Business Applications
|             | 7.3      | LVM    | Agente Linux | RHEL 7,3 para SAP Business Applications
RHEL-HA       | 7.4      | RAW    | Agente Linux | RHEL 7,4 com complemento de HA
|             | 7.5      | RAW    | Agente Linux | RHEL 7,5 com complemento de HA
|             | 7,6      | RAW    | Agente Linux | RHEL 7,6 com complemento de HA
RHEL-SAP-HA   | 7.4      | RAW    | Agente Linux | RHEL 7,4 para SAP com complemento de HA
|             | 7.5      | RAW    | Agente Linux | RHEL 7,5 para SAP com complemento de HA
|             | 7,6      | RAW    | Agente Linux | RHEL 7,6 para SAP com complemento de HA

### <a name="old-naming-convention"></a>Convenção de nomenclatura antiga
A família de imagens RHEL 7 e a família de imagens RHEL 6 usavam versões específicas em seus SKUs até a alteração da Convenção de nomenclatura explicada acima.

Você encontrará SKUs numéricos na lista de imagens inteiras. A Microsoft e a Red Hat criarão novos SKUs numéricos quando uma nova versão secundária for lançada.

### <a name="other-available-offers-and-skus"></a>Outras ofertas e SKUs disponíveis
A lista completa de ofertas e SKUs disponíveis pode incluir imagens adicionais além das listadas na tabela acima, por exemplo, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Essas ofertas podem ser usadas para fornecer suporte a soluções específicas do Marketplace ou podem ser publicadas para visualizações e fins de teste. Eles podem ser alterados ou removidos a qualquer momento, sem aviso. Não os use, a menos que sua presença tenha sido documentada publicamente pela Microsoft ou Red Hat.

## <a name="publishing-policy"></a>Política de publicação
As imagens de atualização do Microsoft e do Red Hat como novas versões secundárias são lançadas, conforme necessário para tratar CVEs específicos ou para alterações/atualizações de configuração ocasionais. Nos esforçamos para fornecer imagens atualizadas assim que possível-dentro de três dias úteis após uma versão ou disponibilidade de uma correção de CVE.

Atualizamos apenas a versão secundária atual em uma determinada família de imagens. Com o lançamento de uma versão secundária mais recente, deixamos de atualizar a versão secundária mais antiga. Por exemplo, com o lançamento do RHEL 7,6, as imagens do RHEL 7,5 não serão mais atualizadas.

>[!NOTE]
> As VMs ativas do Azure provisionadas a partir de imagens pré-pagas do RHEL estão conectadas à RHUI do Azure e podem receber atualizações e correções assim que são lançadas pelo Red Hat e replicadas para o RHUI do Azure (geralmente em menos de 24 horas após o lançamento oficial por Red Hat) . Essas VMs não exigem uma nova imagem publicada para obter as atualizações e os clientes têm controle total sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem
Nossa política atual é manter todas as imagens publicadas anteriormente. Reservamos o direito de remover imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido a atualizações de componentes ou plataformas subsequentes podem ser removidas. As imagens que podem ser removidas seguirão a política atual do Marketplace para fornecer notificações de até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre a infraestrutura de atualização do Red Hat [aqui](https://aka.ms/rhui-update).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
