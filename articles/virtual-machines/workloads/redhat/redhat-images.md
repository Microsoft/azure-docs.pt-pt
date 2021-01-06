---
title: Visão geral das imagens red hat enterprise Linux em Azure
description: Saiba mais sobre as imagens Red Hat Enterprise Linux no Microsoft Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: dbff136ac5787ef9549cca9dc8f946c56a138986
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900506"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Visão geral das imagens do Red Hat Enterprise Linux

Este artigo descreve imagens disponíveis da Red Hat Enterprise Linux (RHEL) no Azure Marketplace e políticas em torno do seu nome e retenção.

Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte o ciclo de [vida da Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata). Para obter detalhes sobre os preços, consulte [a calculadora de preços da Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)

>[!IMPORTANT]
> As imagens RHEL atualmente disponíveis no suporte do Azure Marketplace ou trazem os modelos de licenciamento de bring-your-your-own -subscription (BYOS) ou pay-as-you-go. A comutação dinâmica entre BYOS e o licenciamento pay-as-you-go pode ser feita através do [Azure Hybrid Use Benefit](../../linux/azure-hybrid-benefit-linux.md).

>[!NOTE]
> Para qualquer problema relacionado com imagens RHEL no Azure Marketplace, preencha um bilhete de suporte com a Microsoft.

## <a name="view-images-available-in-azure"></a>Ver imagens disponíveis em Azure

Quando pesquisar "Chapéu Vermelho" no Azure Marketplace ou quando criar um recurso no portal Azure UI, verá apenas um subconjunto de todas as imagens RHEL disponíveis. Pode sempre obter o conjunto completo de imagens VM disponíveis utilizando o Azure CLI, PowerShell e API.

Para ver o conjunto completo de imagens disponíveis do Red Hat em Azure, executar o seguinte comando:

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenção de nomeação

As imagens VM em Azure são organizadas pela editora, oferta, SKU e versão. A combinação de Editor:Offer:SKU:Versão é a imagem URN e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:8-LVM:8.1.20200318` refere-se a uma imagem partida LVM 8.1 LVM construída em 18 de março de 2020.

Uma amostra de como criar um RHEL 8.1 VM é mostrada aqui.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:8.1.20200318 --no-wait
```

### <a name="the-latest-moniker"></a>O apelido "mais recente"

A Azure REST API permite a utilização do apelido "mais recente" para a versão em vez da versão específica. Utilizando as mais recentes disposições, a mais recente imagem disponível para o editor, oferta e SKU.

Por exemplo, `RedHat:RHEL:8-LVM:latest` refere-se à mais recente imagem lvm-partition da família RHEL 8 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:8-LVM:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação das versões para determinar as últimas segue as regras do [método CompareTo](/dotnet/api/system.version.compareto?view=netcore-3.1#system_version_compareto_system_version_).
Esta comparação de versão de imagem é feita comparando os valores como um objeto [versão,](/dotnet/api/system.version.-ctor?view=netframework-4.8) e não como uma corda.

## <a name="rhel-6-image-types"></a>TIPOS DE IMAGEM RHEL 6

Para as imagens RHEL 6.x, os tipos de imagem são apresentados na tabela seguinte.

|Publisher | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 6.9) | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 6.9.2018010506) | Todas as imagens padrão rhel 6.x seguem esta convenção.
|RedHat | rhel-byos | rhel-raw69 | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 6.9.20181023) | Esta imagem é uma imagem RHEL 6.9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 6.8.2017053118) | Esta imagem é um RHEL 6.8 para imagem de aplicações SAP. Tem direito a aceder a repositórios de aplicações SAP e repositórios RHEL de base.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 6.7.2017053121) | Esta imagem é um RHEL 6.7 para a imagem SAP HANA. Tem o direito de aceder a repositórios SAP HANA e repositórios RHEL de base.

## <a name="rhel-7-image-types"></a>TIPOS DE IMAGEM RHEL 7

Para imagens RHEL 7.x, existem alguns tipos de imagem diferentes. A tabela seguinte mostra os diferentes conjuntos de imagens que oferecemos. Para ver uma lista completa, utilize o comando Azure CLI `az vm image list --publisher redhat --all` .

>[!NOTE]
> Salvo indicação em contrário, todas as imagens são divididas em LVM e conectam-se a repositórios RHEL regulares. Ou seja, os repositórios não são suporte de atualização alargado (EUS) e não são serviços de atualização para SAP (E4S). Daqui para a frente, estamos a avançar para a publicação de apenas imagens partidas pela LVM, mas estamos abertos a feedback sobre esta decisão. Para obter mais informações sobre serviços de suporte e atualização de atualização alargada para SAP, consulte [o ciclo de vida da Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).

|Publisher | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 7.6) | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.6.2019102813) | As imagens publicadas antes de abril de 2019 estão anexadas aos repositórios padrão da RHEL. As imagens publicadas depois de abril de 2019 estão anexadas aos repositórios EUS da Red Hat para permitir o bloqueio de versão de uma versão menor específica. Os clientes que pretendam repositórios regulares devem utilizar as imagens que contenham 7-LVM ou 7-RAW no valor SKU (detalhes a seguir). RHEL 7.7 e imagens posteriores são divisórias LVM. Todas as outras imagens desta categoria são divididas em bruto.
|RedHat | RHEL | 7-RAW | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.6.2019102813) | Estas imagens são cruas divididas (por exemplo, não foram adicionados volumes lógicos).
|RedHat | RHEL | 7-RAW-CI | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.6.2019072418) | Estas imagens são divididas em bruto (por exemplo, não foram adicionados volumes lógicos) e usam a nuvem para o provisionamento.
|RedHat | RHEL | 7-LVM | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.6.2019062414) | Estas imagens são divisórias LVM.
|RedHat | rhel-byos | rhel-{lvm,cru} | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.7.20190819) | Estas imagens são as imagens RHEL 7 BYOS. Não estão ligados a nenhum repositório e não cobram a taxa de prémio RHEL. Se estiver interessado nas imagens RHEL BYOS, [solicite acesso](https://aka.ms/rhel-byos). Os valores SKU terminam com a versão menor e denotam se a imagem é crua ou lvm partitioned. Por exemplo, um valor SKU de rhel-lvm77 indica uma imagem RHEL 7.7 partida em LVM.
|RedHat | RHEL | RHEL-SAP | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.6.2019071300) | Estas imagens são RHEL para imagens SAP. Têm direito a aceder aos repositórios SAP HANA e Applications, bem como aos repositórios RHEL E4S. A faturação inclui o prémio RHEL e o prémio SAP para além da taxa de cálculo base.
|RedHat | RHEL | RHEL-SAP-HA | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.6.2019062320) | Estas imagens são RHEL para SAP com imagens de Serviços de Alta Disponibilidade e Atualização. Têm direito a aceder aos repositórios SAP HANA e Applications e aos repositórios de Alta Disponibilidade, bem como aos repositórios RHEL E4S. A faturação inclui o prémio RHEL, o prémio SAP e o prémio de Alta Disponibilidade para além da taxa de cálculo base.
|RedHat | RHEL | RHEL-HA | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.6.2019062019) | Estas imagens são imagens RHEL que também têm direito a aceder ao complemento de Alta Disponibilidade. Cobram um pouco mais em cima da taxa de cálculo RHEL e base devido ao prémio adicional de Alta Disponibilidade.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.3.2017053118) | Estas imagens estão desatualizadas porque as aplicações SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Estas imagens são RHEL para imagens de Aplicações SAP. Têm direito a aceder a repositórios de aplicações SAP e repositórios RHEL de base.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 7.3.2018051421) | Estas imagens estão desatualizadas porque as aplicações SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Estas imagens são RHEL para imagens SAP HANA. Têm direito a aceder a repositórios SAP HANA e repositórios RHEL de base.

## <a name="rhel-8-image-types"></a>TIPOS DE IMAGEM RHEL 8

>[!NOTE]
> A Red Hat recomenda a utilização de Grubby para configurar os parâmetros da linha de comando do núcleo em RHEL 8+. Mais detalhes estão disponíveis [aqui.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)

Os detalhes para os tipos de imagem RHEL 8 estão abaixo.

|Publisher | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 8.0.20191023) | Estas imagens são imagens partidas pela RHEL 8 LVM ligadas aos repositórios padrão do Chapéu Vermelho.
|RedHat | RHEL | 8-gen2 | Valores concatenados da versão menor do RHEL e da data publicada (por exemplo, 8.0.20191024) | Estas imagens são imagens de hiper-V geração 2 RHEL 8 LVM-partitioned connected to red hat repositórios padrão. Para obter mais informações sobre os VMs da Geração 2 em Azure, consulte [Suporte para Azure](../../generation-2.md).

## <a name="rhel-longer-support-add-ons"></a>Addons de suporte mais longos RHEL

### <a name="extended-update-support"></a>Suporte de atualização alargada

A partir de abril de 2019, estão disponíveis imagens RHEL que estão anexadas aos repositórios da UE por defeito. Mais informações sobre o EUS RHEL estão disponíveis na [documentação da Red Hat.](https://access.redhat.com/articles/rhel-eus)

A passagem para repositórios DA Émus é possível e é apoiada. Para obter instruções sobre como mudar o seu VM para EUS e mais informações sobre as datas de fim de vida do SUPORTE EUS, consulte [o RHEL EUS e os VM RHEL que bloqueiam a versão.](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)

>[!NOTE]
> A EUS não é apoiada em extras RHEL. Isto significa que se instalar um pacote que está normalmente disponível no canal RHEL Extras, não poderá fazê-lo enquanto estiver na UES. Para obter mais informações sobre o ciclo de vida do produto Red Hat Extras, consulte o ciclo de [vida da Red Hat Enterprise Linux Extras](https://access.redhat.com/support/policy/updates/extras/).

#### <a name="differentiate-between-regular-and-eus-images"></a>Diferenciar entre imagens regulares e eus

Os clientes que pretendam utilizar imagens anexadas aos repositórios EUS devem utilizar a imagem RHEL que contém um número de versão menor RHEL no SKU.

Por exemplo, pode ver as seguintes duas imagens RHEL 7.4 disponíveis.

```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```

Neste caso, `RedHat:RHEL:7.6:7.6.2019102813` é anexado aos repositórios da UE por defeito. O valor SKU é de 7.4. E `RedHat:RHEL:7-LVM:7.6.2019062414` está ligado a repositórios não-EUS por defeito. O valor SKU é 7-LVM.

Para utilizar repositórios regulares (não-EUS), utilize uma imagem que não contenha um número de versão menor no SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS

As informações no quadro seguinte aplicam-se às imagens RHEL que estão ligadas aos repositórios EUS.

>[!NOTE]
> No momento da escrita, apenas as versões RHEL 7.4 e posteriormente menores têm apoio eus. A EUS já não é apoiada para o <RHEL =7.3.
>
> Para obter mais informações sobre a disponibilidade da RHEL EUS, consulte [o ciclo de vida da Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)

Versão secundária |Exemplo de imagem eus              |Estatuto DA UE                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | As imagens publicadas em abril de 2019 e mais tarde são EUS por defeito.|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | As imagens publicadas em junho de 2019 e posteriormente são EUS por defeito. |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | As imagens publicadas em maio de 2019 e posteriormente são EUS por defeito. |
RHEL 8.0      |N/D                            | Não há EUS disponível na Red Hat.                               |

### <a name="update-services-for-sap"></a>Serviços de atualização para SAP

O mais recente RHEL para imagens SAP será ligado aos Serviços de Atualização para assinaturas SAP Solutions (E4S). Para obter mais informações sobre o E4S, consulte a [documentação](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)do Chapéu Vermelho.

#### <a name="rhel-images-with-e4s"></a>Imagens RHEL com E4S

As imagens das seguintes ofertas criadas após dezembro de 2019 estão ligadas aos repositórios E4S:

* RHEL-SAP (RHEL for SAP)
* RHEL-SAP-HA (RHEL para SAP com Serviços de Alta Disponibilidade e Atualização)

## <a name="other-available-offers-and-skus"></a>Outras ofertas disponíveis e SKUs

A lista completa de ofertas disponíveis e SKUs pode incluir imagens adicionais para além do que está listado na tabela anterior. Um exemplo é `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Estas ofertas podem ser utilizadas para apoiar soluções específicas de mercado. Ou podem ser publicados para pré-visualizações e testes. Podem ser alterados ou removidos a qualquer momento sem aviso prévio. Não os utilize a menos que a sua presença seja documentada publicamente pela Microsoft ou pela Red Hat.

## <a name="publishing-policy"></a>Política de publicação

As imagens de atualização da Microsoft e do Red Hat à medida que forem lançadas novas versões menores, conforme necessário para abordar vulnerabilidades e exposições comuns específicas (CVEs) ou para alterações ou atualizações ocasionais de configuração. Esforçamo-nos por fornecer imagens atualizadas o mais rapidamente possível no prazo de três dias úteis após o lançamento ou disponibilidade de uma correção CVE.

Atualizamos apenas a versão menor atual numa dada família de imagens. Com o lançamento de uma versão menor mais recente, paramos de atualizar a versão menor mais antiga. Por exemplo, com o lançamento do RHEL 7.6, as imagens RHEL 7.5 já não são atualizadas.

>[!NOTE]
> Os VMs ativos azure a partir de imagens pay-as-you-go da RHEL estão ligados ao Azure RHUI e podem receber atualizações e correções assim que forem lançados pela Red Hat e replicados no Azure RHUI. O tempo é geralmente menos de 24 horas após o lançamento oficial da Red Hat. Estes VMs não requerem uma nova imagem publicada para obter as atualizações. Os clientes têm controlo total sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem

A política atual é manter todas as imagens publicadas anteriormente. Reservamo-nos o direito de remover imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, imagens com configurações incorretas devido a atualizações subsequentes da plataforma ou componentes podem ser removidas. As imagens que podem ser removidas seguem a atual política do Azure Marketplace para fornecer notificações até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Passos seguintes

* Para ver a lista completa de imagens RHEL em Azure, consulte [as imagens red hat enterprise Linux (RHEL) disponíveis em Azure](./redhat-imagelist.md).
* Para saber mais sobre a Infraestrutura de Atualização do Chapéu Vermelho Azure, consulte [a Infraestrutura red hat update para VMs RHEL a pedido em Azure](./redhat-rhui.md).
* Para saber mais sobre a oferta DA RHEL BYOS, consulte [Red Hat Enterprise Linux bring-your-your-own-subscription Gold Images in Azure](./byos.md).
* Para obter informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL, consulte o ciclo de [vida da Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).