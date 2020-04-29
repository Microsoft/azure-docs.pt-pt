---
title: Imagens red hat enterprise linux em Azure Microsoft Docs
description: Conheça as imagens do Red Hat Enterprise Linux no Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: f06c4304be67fbc2f3116375dae33b10228723a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80239866"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Visão geral das imagens do Red Hat Enterprise Linux

Este artigo descreve imagens disponíveis da Red Hat Enterprise Linux (RHEL) no Azure Marketplace e políticas em torno do seu nome e retenção.

Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte o ciclo de [vida red hat enterprise linux](https://access.redhat.com/support/policy/updates/errata). Para obter detalhes sobre preços, consulte a calculadora de [preços do Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)

>[!IMPORTANT]
> As imagens RHEL atualmente disponíveis no Azure Marketplace suportam modelos de licenciamento por subscrição própria (BYOS) ou pay-as-you-go. O [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) e a mudança dinâmica entre byos e licenciamento pay-as-you-go não são suportados. Para mudar o modo de licenciamento, tem de recolocar o VM a partir da imagem correspondente.

>[!NOTE]
> Para qualquer problema relacionado com imagens RHEL no Azure Marketplace, preencha um bilhete de suporte com a Microsoft.

## <a name="view-images-available-in-azure"></a>Ver imagens disponíveis em Azure

Quando pesquisar por "Chapéu Vermelho" no Azure Marketplace ou quando criar um recurso no portal Azure UI, verá apenas um subconjunto de todas as imagens RHEL disponíveis. Pode sempre obter o conjunto completo de imagens VM disponíveis utilizando o Azure CLI, PowerShell e API.

Para ver o conjunto completo de imagens do Chapéu Vermelho disponíveis em Azure, execute o seguinte comando:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenção de nomeação

As imagens VM em Azure são organizadas pela editora, oferta, SKU e versão. A combinação de Publisher:Offer:SKU:Version é a URN de imagem e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:8-LVM:8.1.20200318` refere-se a uma imagem com divisão RHEL 8.1 LVM construída em 18 de março de 2020.

Uma amostra de como criar um RHEL 8.1 VM é mostrada aqui.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>O apelido "mais recente"

O Azure REST API permite a utilização do apelido "mais recente" para a versão em vez da versão específica. Utilizando as "mais recentes" disposições, a mais recente imagem disponível para o editor, oferta e SKU.

Por exemplo, `RedHat:RHEL:8-LVM:latest` refere-se à mais recente imagem lLm-partition da família RHEL 8 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar as mais recentes segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Esta comparação de versão de imagem é feita comparando os valores como um objeto [versão,](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) não como uma cadeia.

## <a name="rhel-6-image-types"></a>Tipos de imagem RHEL 6

Para imagens RHEL 6.x, os tipos de imagem são mostrados na tabela seguinte.

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 6.9) | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 6.9.2018010506) | Todas as imagens padrão rHEL 6.x seguem esta convenção.
|RedHat | rhel-byos | rhel-raw69 | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 6.9.20181023) | Esta imagem é uma imagem RHEL 6.9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 6.8.2017053118) | Esta imagem é um RHEL 6.8 para a imagem de Aplicações SAP. Tem direito a aceder aos repositórios de aplicações SAP e repositórios base RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 6.7.2017053121) | Esta imagem é um RHEL 6.7 para a imagem SAP HANA. Tem direito a aceder aos repositórios SAP HANA e aos repositórios base RHEL.

## <a name="rhel-7-image-types"></a>Tipos de imagem RHEL 7

Para imagens RHEL 7.x, existem alguns tipos de imagem diferentes. A tabela que se segue mostra os diferentes conjuntos de imagens que oferecemos. Para ver uma lista completa, utilize `az vm image list --publisher redhat --all`o comando Azure CLI .

>[!NOTE]
> Salvo indicação em contrário, todas as imagens são divididas em LVM e conectam-se aos repositórios RHEL regulares. Ou seja, os repositórios não são Suporte de Atualização Estendida (EUS) e não são Serviços de Atualização para SAP (E4S). Daqui para a frente, estamos a avançar para a publicação apenas de imagens divididas pela LVM, mas estamos abertos a feedback sobre esta decisão. Para obter mais informações sobre suporte de atualização estendida e serviços de atualização para SAP, consulte o ciclo de [vida da Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 7.6) | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019102813) | As imagens publicadas antes de abril de 2019 estão anexadas aos repositórios rhel padrão. As imagens publicadas após abril de 2019 estão anexadas aos repositórios eus da Red Hat para permitir o bloqueio de versão de uma versão menor específica. Os clientes que pretendam repositórios regulares devem utilizar as imagens que contenham 7-LVM ou 7-RAW no valor SKU (os detalhes seguem). RHEL 7.7 e imagens posteriores são divididas lvm. Todas as outras imagens desta categoria são divididas em bruto.
|RedHat | RHEL | 7-RAW | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019102813) | Estas imagens são divididas em bruto (por exemplo, não foram adicionados volumes lógicos).
|RedHat | RHEL | 7-RAW-CI | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019072418) | Estas imagens são divididas em bruto (por exemplo, não foram adicionados volumes lógicos) e utilizam cloud-init para o fornecimento.
|RedHat | RHEL | 7-LVM | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019062414) | Estas imagens são divididas em LVM.
|RedHat | rhel-byos | rhel-{lvm,cru} | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.7.20190819) | Estas imagens são as imagens RHEL 7 BYOS. Não estão ligados a nenhum repositório e não cobram a taxa de prémio RHEL. Se estiver interessado nas imagens RHEL BYOS, [solicite acesso](https://aka.ms/rhel-byos). Os valores de SKU terminam com a versão menor e denotam se a imagem é crua ou a lvm dividida. Por exemplo, um valor SKU de rhel-lvm77 indica uma imagem RHEL 7.7 com divisão LVM.
|RedHat | RHEL | RHEL-SAP | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019071300) | Estas imagens são RHEL para imagens SAP. Têm direito a aceder aos repositórios SAP HANA e Aplicações, bem como aos repositórios RHEL E4S. A faturação inclui o prémio RHEL e o prémio SAP para além da taxa de cálculo base.
|RedHat | RHEL | RHEL-SAP-HA | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019062320) | Estas imagens são RHEL para SAP com imagens de Alta Disponibilidade e Update Services. Têm direito a aceder aos repositórios SAP HANA e Aplicações e aos repositórios de alta disponibilidade, bem como aos repositórios RHEL E4S. A faturação inclui o prémio RHEL, o prémio SAP e o prémio de Alta Disponibilidade para além da taxa de cálculo base.
|RedHat | RHEL | RHEL-HA | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019062019) | Estas imagens são imagens RHEL que também têm direito a aceder ao addon de alta disponibilidade. Cobram um pouco mais em cima da taxa de computação RHEL e base devido ao prémio adicional de alta disponibilidade.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.3.2017053118) | Estas imagens estão desatualizadas porque as aplicações SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Estas imagens são RHEL para imagens de Aplicações SAP. Têm direito a aceder aos repositórios de aplicações SAP e repositórios base RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.3.2018051421) | Estas imagens estão desatualizadas porque as aplicações SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Estas imagens são RHEL para imagens SAP HANA. Têm direito a aceder aos repositórios SAP HANA e aos repositórios base RHEL.

## <a name="rhel-8-image-types"></a>Tipos de imagem RHEL 8

Os detalhes para os tipos de imagem RHEL 8 estão abaixo.

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 8.0.20191023) | Estas imagens são imagens com divisão RHEL 8 LVM ligadas aos repositórios padrão do Chapéu Vermelho.
|RedHat | RHEL | 8 gen2 | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 8.0.20191024) | Estas imagens são imagens com divisão de Hiper-V Geração 2 RHEL 8 LVM ligadas aos repositórios padrão do Chapéu Vermelho. Para mais informações sobre os VMs da Geração 2 em Azure, consulte [Suporte para VMs da Geração 2 no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2).

## <a name="rhel-longer-support-add-ons"></a>Add-ons de suporte mais longor RHEL

### <a name="extended-update-support"></a>Suporte de atualização estendida

A partir de abril de 2019, estão disponíveis imagens RHEL que estão anexadas aos repositórios da EUS por defeito. Mais informações sobre o RHEL EUS estão disponíveis na [documentação da Red Hat.](https://access.redhat.com/articles/rhel-eus)

A mudança para os repositórios da UES é possível e é apoiada. Para obter instruções sobre como mudar o seu VM para EUS e mais informações sobre datas de fim de vida de suporte da UES, consulte [o RHEL EUS e os VMs rHEL de bloqueio](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)de versão.

>[!NOTE]
> A EUS não é apoiada em Extras RHEL. Isto significa que se instalar um pacote que normalmente está disponível no canal RHEL Extras, não poderá fazê-lo enquanto estiver no EUS. Para obter mais informações sobre o ciclo de vida do produto Red Hat Extras, consulte o ciclo de [vida da Red Hat Enterprise Linux Extras.](https://access.redhat.com/support/policy/updates/extras/)

#### <a name="differentiate-between-regular-and-eus-images"></a>Diferenciar entre imagens regulares e eus

Os clientes que pretendam utilizar imagens anexas aos repositórios eus devem utilizar a imagem RHEL que contém um número de versão rHEL menor no SKU.

Por exemplo, pode ver as seguintes duas imagens RHEL 7.4 disponíveis.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

Neste caso, `RedHat:RHEL:7.6:7.6.2019102813` está anexado por defeito aos repositórios da EUS. O valor SKU é de 7.4. E `RedHat:RHEL:7-LVM:7.6.2019062414` está ligado a repositórios não-EUS por defeito. O valor SKU é 7-LVM.

Para utilizar repositórios regulares (não-EUS), utilize uma imagem que não contenha um número de versão menor no SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS

As informações no quadro seguinte aplicam-se às imagens RHEL que estão ligadas aos repositórios da EUS.

>[!NOTE]
> No momento da escrita, apenas as versões RHEL 7.4 e posteriores menores têm apoio da UES. A UES já não é apoiada por RHEL <=7,3.
>
> Para obter mais informações sobre a disponibilidade do RHEL EUS, consulte o ciclo de [vida da Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

Versão menor |Exemplo de imagem da UES              |Estatuto da UES                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | As imagens publicadas abril de 2019 e posteriormente são eus por defeito.|
RHEL 7.5      |RedHat:RHEL:7.5.5.2019060305 | As imagens publicadas junho de 2019 e posteriormente são eus por defeito. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.201905206 | As imagens publicadas em maio de 2019 e posteriormente são eus por defeito. |
RHEL 8.0      |N/D                            | Não existe nenhum EUS disponível na Red Hat.                               |

### <a name="update-services-for-sap"></a>Serviços de Atualização para SAP

Os mais recentes RHEL para imagens SAP serão ligados aos Serviços de Atualização para assinaturas SAP Solutions (E4S). Para mais informações sobre o E4S, consulte a [documentação](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)do Chapéu Vermelho.

#### <a name="rhel-images-with-e4s"></a>Imagens RHEL com E4S

As imagens das seguintes ofertas criadas após dezembro de 2019 estão ligadas aos repositórios E4S:

* RHEL-SAP (RHEL para SAP)
* RHEL-SAP-HA (RHEL para SAP com Serviços de Alta Disponibilidade e Atualização)

## <a name="other-available-offers-and-skus"></a>Outras ofertas disponíveis e SKUs

A lista completa de ofertas disponíveis e SKUs pode incluir imagens adicionais para além do que está listado na tabela anterior. Um exemplo é `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Estas ofertas podem ser utilizadas para fornecer apoio a soluções específicas de mercado. Ou podem ser publicados para pré-visualizações e testes. Podem ser alterados ou removidos a qualquer momento sem aviso prévio. Não os utilize a menos que a sua presença seja documentada publicamente pela Microsoft ou pela Red Hat.

## <a name="publishing-policy"></a>Política editorial

A Microsoft e o Red Hat atualizam as imagens à medida que são lançadas novas versões menores, conforme necessário para abordar vulnerabilidades e exposições comuns específicas (CVEs) ou para alterações ou atualizações ocasionais de configuração. Esforçamo-nos por fornecer imagens atualizadas o mais rapidamente possível no prazo de três dias úteis após o lançamento ou disponibilidade de uma correção CVE.

Atualizamos apenas a versão menor atual numa dada família de imagens. Com o lançamento de uma versão menor mais recente, deixamos de atualizar a versão menor mais antiga. Por exemplo, com o lançamento de Imagens RHEL 7.6, as imagens RHEL 7.5 deixaram de ser atualizadas.

>[!NOTE]
> Os VMs Ative Azure, a partir de imagens pay-as-you-go da RHEL, estão ligados ao Azure RHUI e podem receber atualizações e correções assim que forem lançados pela Red Hat e replicados ao Azure RHUI. O momento é geralmente inferior a 24 horas após o lançamento oficial pela Red Hat. Estes VMs não requerem uma nova imagem publicada para obter as atualizações. Os clientes têm total controlo sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem

A política atual é manter todas as imagens anteriormente publicadas. Reservamo-nos o direito de remover imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido a atualizações de plataforma ou componentes subsequentes podem ser removidas. As imagens que podem ser removidas seguem a atual política do Azure Marketplace para fornecer notificações até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Passos seguintes

* Para ver a lista completa de imagens RHEL em Azure, consulte [as imagens Red Hat Enterprise Linux (RHEL) disponíveis no Azure](./redhat-imagelist.md).
* Para saber mais sobre a Infraestrutura de Atualização do Chapéu Vermelho Azure, consulte a [Red Hat Update Infrastructure para VMs RHEL on-demand em Azure](https://aka.ms/rhui-update).
* Para saber mais sobre a oferta rHEL BYOS, consulte [Red Hat Enterprise Linux bring-your-your-own-subscription Gold Images in Azure](./byos.md).
* Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte o ciclo de [vida red hat enterprise linux](https://access.redhat.com/support/policy/updates/errata).
