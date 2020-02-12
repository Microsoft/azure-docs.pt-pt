---
title: Imagens red hat enterprise linux em Azure Microsoft Docs
description: Conheça as imagens do Red Hat Enterprise Linux no Microsoft Azure
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
ms.openlocfilehash: 5ed5d9337dd4e7acdbba25c4cb66d2690793f250
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134394"
---
# <a name="overview-of-red-hat-enterprise-linux-images"></a>Visão geral das imagens do Red Hat Enterprise Linux
Este artigo descreve imagens disponíveis da Red Hat Enterprise Linux (RHEL) no Mercado Azure, juntamente com políticas em torno do seu nome e retenção.

As informações sobre as políticas de suporte do Chapéu Vermelho para todas as versões do RHEL podem ser encontradas na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata) Para mais detalhes sobre preços, visite a Calculadora de [Preços Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)

>[!IMPORTANT]
> As imagens RHEL atualmente disponíveis no mercado Azure suportam os modelos de licenciamento Bring-Your-Your-Own-Subscription (BYOS) ou Pay-As-You-Go (PAYG). O [Azure Hybrid Use Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) e a mudança dinâmica entre BYOS e PAYG não são suportadas. A comutação do modo de licenciamento requer a recolocação do VM a partir da imagem correspondente.

>[!NOTE]
> Para qualquer problema relacionado com imagens RHEL no mercado Azure, por favor preencha um bilhete de suporte com a Microsoft.

## <a name="viewing-images-available-in-azure"></a>Ver imagens disponíveis em Azure
Quando pesquisar por "Chapéu Vermelho" no Mercado ou quando criar um recurso no portal Azure UI, verá apenas um subconjunto de todas as imagens RHEL disponíveis. Pode sempre obter o conjunto completo de imagens VM disponíveis utilizando o Azure CLI/PowerShell/API.

Para ver o conjunto completo de imagens de Chapéu Vermelho disponíveis em Azure, corra o seguinte comando

```azurecli-interactive
az vm image list --publisher RedHat --all
```

### <a name="naming-convention"></a>Convenção de nomeação
As imagens VM em Azure são organizadas pela Publisher, Offer, SKU e Versão. A combinação de Publisher:Offer:SKU:Version é a URN de imagem e identifica exclusivamente a imagem a ser usada.

Por exemplo, `RedHat:RHEL:7-LVM:7.6.2018103108` refere-se a uma imagem com divisão RHEL 7.6 LVM construída a 31 de outubro de 2018.

Uma amostra de como criar um RHEL 7.6 VM é mostrada abaixo.
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>O apelido "mais recente"
O Azure REST API permite a utilização de apelido "mais recente" para versão em vez da versão específica. A utilização de "mais recentes" fornecerá a mais recente imagem disponível para a dada Editora, Oferta e SKU.

Por exemplo, `RedHat:RHEL:7-LVM:latest` refere-se à mais recente imagem lLm-partition da família RHEL 7 disponível.

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-LVM:latest --no-wait
```

>[!NOTE]
> Em geral, a comparação de versões para determinar as mais recentes segue as regras do [método CompareTo](https://msdn.microsoft.com/library/a5ts8tb6.aspx).
Esta comparação de versão de imagem é feita comparando os valores como um objeto [versão](https://docs.microsoft.com/dotnet/api/system.version.-ctor?view=netframework-4.8) - não como uma cadeia.

## <a name="rhel-6-image-types"></a>Tipos de imagem RHEL 6
Para imagens RHEL 6.x, os tipos de imagem são os seguintes:

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|-----------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 6.9) | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 6.9.2018010506) | Todas as imagens padrão rHEL 6.x seguem esta convenção
|RedHat | rhel-byos | rhel-raw69 | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 6.9.20181023) | Esta imagem é uma imagem RHEL 6.9 BYOS.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 6.8.2017053118) | Este é um RHEL 6.8 para a imagem de Aplicações SAP. Tem direito a aceder aos repositórios de aplicações SAP, bem como aos repositórios base RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 6.7.2017053121) | Este é um RHEL 6.7 para a imagem SAP HANA. Tem direito a aceder aos repositórios SAP HANA, bem como aos repositórios base RHEL.

## <a name="rhel-7-image-types"></a>Tipos de imagem RHEL 7
Para imagens RHEL 7.x, existem alguns tipos de imagem diferentes. A tabela que se segue mostra os diferentes conjuntos de imagens que oferecemos. Uma lista completa pode ser visualizada com o comando Az CLI `az vm image list --publisher redhat --all`.

>[!NOTE]
> Salvo indicação em contrário, todas as imagens são divididas por LVM e ligar-se-ão aos repositórios rhel regulares (ou seja, não EUS, não E4S). Para a frente, estamos a avançar para a publicação apenas de imagens com divisão de LVM, mas estamos abertos a feedback sobre esta decisão. Os detalhes sobre suporte de atualização alargado e serviços de atualização para SAP estão disponíveis na [página red hat enterprise linux Life Cycle](https://access.redhat.com/support/policy/updates/errata).

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | Versão menor (por exemplo, 7.6) | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019102813) | As imagens publicadas antes de abril de 2019 serão anexadas aos repositórios rhel padrão. As imagens publicadas após abril de 2019 serão anexadas aos repositórios de Suporte de Atualização Alargada (EUS) da Red Hat para permitir o bloqueio de versão de uma versão menor específica. Os clientes que pretendam repositórios regulares devem utilizar as imagens que contenham 7-LVM ou 7-RAW no valor SKU (detalhes abaixo). RHEL 7.7 e imagens posteriores serão divididas por LVM. Todas as outras imagens desta categoria são divididas em bruto.
|RedHat | RHEL | 7-RAW | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019102813) | Estas imagens são divididas em bruto (isto é, não foram adicionados volumes lógicos).
|RedHat | RHEL | 7-RAW-CI | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019072418) | Estas imagens são divididas em bruto (ou seja, não foram adicionados volumes lógicos), e utilizarão cloud-init para o fornecimento.
|RedHat | RHEL | 7-LVM | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 7.6.2019062414) | Estas imagens são divididas por LVM.
|RedHat | rhel-byos | rhel-{lvm,cru} | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.7.20190819) | Estas imagens são as imagens RHEL 7 BYOS. THey não estão ligados a quaisquer repositórios e não cobrarão a taxa de prémio RHEL. Se estiver interessado nas imagens RHEL BYOS, [solicite acesso](https://aka.ms/rhel-byos). Os valores de SKU terminam com a versão menor e denotam se a imagem é crua ou dividida por LVM. Por exemplo, um valor SKU de rhel-lvm77 indica uma imagem RHEL 7.7 com divisão LVM.
|RedHat | RHEL | RHEL-SAP | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 7.6.2019071300) | Estas imagens são RHEL para imagens SAP. Têm direito a aceder aos repositórios SAP HANA e Aplicações, bem como aos repositórios RHEL E4S. A faturação inclui o prémio RHEL e o prémio SAP para além da taxa de cálculo base.
|RedHat | RHEL | RHEL-SAP-HA | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 7.6.2019062320) | Estas imagens são RHEL para SAP com imagens de Alta Disponibilidade e Update Services. Têm direito a aceder aos repositórios SAP HANA e Aplicações e aos repositórios de alta disponibilidade, bem como aos repositórios RHEL E4S. A faturação inclui o prémio RHEL, o prémio SAP e o prémio HA em cima da taxa de cálculo base.
|RedHat | RHEL | RHEL-HA | Valores concatenados da versão rHEL menor e data publicada (por exemplo, 7.6.2019062019) | Estas são imagens RHEL que também têm direito a aceder ao addon de alta disponibilidade. Cobrarão um pouco mais em cima da taxa de computação RHEL e base devido ao prémio adicional ha.
|RedHat | RHEL | RHEL-SAP-APPS | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 7.3.2017053118) | Estas imagens estão desatualizadas à medida que as aplicações SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Estes são Imagens RHEL para Aplicações SAP. Têm direito a aceder aos repositórios de aplicações SAP, bem como aos repositórios base RHEL.
|RedHat | RHEL | RHEL-SAP-HANA | Valores concatenados da versão rHEL menor e da data publicada (por exemplo, 7.3.2018051421) | Estas imagens estão desatualizadas à medida que as aplicações SAP e os repositórios SAP HANA foram combinados nos repositórios SAP. Estes são RHEL para imagens SAP HANA. Têm direito a aceder aos repositórios SAP HANA, bem como aos repositórios base RHEL.

## <a name="rhel-8-image-types"></a>Tipos de imagem RHEL 8
Os detalhes para os tipos de imagem RHEL 8 estão abaixo.

|Publicador | Oferta | Valor SKU | Versão | Detalhes
|----------|-------|------------|---------|--------
|RedHat | RHEL | 8 | Valores concatenados da versão rHEL menor e data publicada (por exemplo 8.0.20191023) | Estas imagens são imagens com divisão RHEL 8.0 LVM ligadas aos repositórios padrão do Chapéu Vermelho.
|RedHat | RHEL | 8 gen2 | Valores concatenados da versão rHEL menor e data publicada (por exemplo 8.0.20191024) | Estas imagens são imagens hiper-V Geração 2 RHEL 8.0 LVM ligadas aos repositórios padrão do Chapéu Vermelho. Mais informações sobre os VMs da Geração 2 em Azure estão disponíveis [aqui.](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)

## <a name="rhel-longer-support-add-ons"></a>Add-ons de suporte mais longor RHEL

### <a name="extended-update-support-eus"></a>Apoio à atualização alargada (EUS)
A partir de abril de 2019, estão disponíveis imagens RHEL que estão anexadas aos repositórios de Suporte de Atualização Estendida (EUS) por defeito. Mais detalhes sobre o RHEL EUS estão disponíveis na [documentação da Red Hat.](https://access.redhat.com/articles/rhel-eus)

A mudança para os repositórios da UES é possível e é apoiada. Estão aqui disponíveis instruções sobre como mudar o seu VM para EUS e estão disponíveis [mais](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)detalhes sobre datas de fim de vida de suporte da UES aqui .

>[!NOTE]
> A EUS não é apoiada em Extras RHEL. Isto significa que, se estiver a instalar um pacote que normalmente está disponível no canal RHEL Extras, não poderá fazê-lo enquanto estiver no EUS. O Ciclo de Vida do Produto Red Hat Extras é detalhado [aqui.](https://access.redhat.com/support/policy/updates/extras/)

#### <a name="differentiating-between-regular-and-eus-images"></a>Diferenciar as imagens regulares e as imagens da UES.
Os clientes que pretendam utilizar imagens anexas aos repositórios eus devem utilizar a imagem RHEL que contém um número de versão rHEL menor no SKU.

Por exemplo, pode ver as seguintes duas imagens RHEL 7.4 disponíveis:
```bash
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7.6:7.6.2019102813
```
Neste caso, `RedHat:RHEL:7.6:7.6.2019102813` serão anexados por defeito aos repositórios da EUS (valor SKU de 7.4), e `RedHat:RHEL:7-LVM:7.6.2019062414` serão anexados por predefinição a repositórios não-EUS (valor SKU de 7-LVM).

Se pretender utilizar repositórios regulares (não-EUS), desdobre-se utilizando uma imagem que não contenha um número de versão menor no SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS
A tabela seguinte aplicar-se-á às imagens RHEL que estão ligadas aos repositórios da EUS.

>[!NOTE]
> No momento da escrita, apenas as versões RHEL 7.4 e posteriores menores têm apoio da UES. A EUS já não é apoiada pela RHEL <= 7,3.
>
> Mais detalhes sobre a disponibilidade do RHEL EUS podem ser consultados [aqui.](https://access.redhat.com/support/policy/updates/errata)

Versão menor |Exemplo de imagem da UES              |Estatuto da UES                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Imagens publicadas abril de 2019 e posteriormente serão EUS por defeito|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Imagens publicadas junho de 2019 e posteriormente serão EUS por defeito |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Imagens publicadas maio de 2019 e posteriormente serão EUS por defeito  |
RHEL 8.0      |N/D                            | Sem EUS disponível a partir da Red Hat                               |

### <a name="update-services-for-sap-e4s"></a>Serviços de Atualização para SAP (E4S)
Os mais recentes RHEL para imagens SAP serão ligados aos Serviços de Atualização para assinaturas SAP Solutions (E4S). Mais detalhes sobre o E4S estão disponíveis na [documentação](https://access.redhat.com/support/policy/updates/errata#Update_Services_for_SAP_Solutions)do Chapéu Vermelho.

#### <a name="rhel-images-with-e4s"></a>Imagens RHEL com E4S
As imagens das seguintes ofertas criadas após dezembro de 2019 estarão ligadas aos repositórios E4S.

* RHEL-SAP (RHEL para SAP)
* RHEL-SAP-HA (RHEL para SAP com HA e Serviços de Atualização)

## <a name="other-available-offers-and-skus"></a>Outras ofertas disponíveis e SKUs
A lista completa de ofertas disponíveis e SKUs pode incluir imagens adicionais para além do que está listado na tabela acima, por exemplo, `RedHat:rhel-ocp-marketplace:rhel74:7.4.1`. Estas ofertas podem ser utilizadas para fornecer suporte a soluções específicas de mercado, ou podem ser publicadas para pré-visualizações e testes. Podem ser alterados ou removidos a qualquer momento sem aviso prévio. Não as utilize a menos que a sua presença tenha sido documentada publicamente pela Microsoft ou pela Red Hat.

## <a name="publishing-policy"></a>Política editorial
Microsoft e Red Hat atualizam as imagens à medida que novas versões menores são lançadas, conforme necessário para abordar CVEs específicos, ou para alterações/atualizações de configuração ocasionais. Esforçamo-nos por fornecer imagens atualizadas o mais rapidamente possível - no prazo de três dias úteis após o lançamento ou disponibilidade de uma correção CVE.

Só atualizamos o lançamento menor atual numa dada família de imagens. Com o lançamento de uma versão menor mais recente, deixamos de atualizar a versão menor mais antiga. Por exemplo, com o lançamento de Imagens RHEL 7.6, as imagens RHEL 7.5 deixarão de ser atualizadas.

>[!NOTE]
> Os VMs Ative Azure, a partir de imagens RHEL Pay-As-You-Go, estão ligados ao Azure RHUI e podem receber atualizações e correções assim que forem lançados pela Red Hat e replicados ao Azure RHUI (geralmente em menos de 24 horas após o lançamento oficial pela Red Hat) . Estes VMs não requerem uma nova imagem publicada para obter as atualizações e os clientes têm total controlo sobre quando iniciar a atualização.

## <a name="image-retention-policy"></a>Política de retenção de imagem
A nossa política atual é manter todas as imagens anteriormente publicadas. Reservamo-nos o direito de remover imagens que são conhecidas por causar problemas de qualquer tipo. Por exemplo, podem ser removidas imagens com configurações incorretas devido a atualizações posteriores da plataforma ou componentes. As imagens que possam ser removidas seguirão a atual política do Marketplace para fornecer notificações até 30 dias antes da remoção da imagem.

## <a name="next-steps"></a>Passos seguintes
* Veja a lista completa de [imagens RHEL em Azure](./redhat-imagelist.md).
* Saiba mais sobre a Infraestrutura de Atualização do Chapéu Vermelho Azure [aqui](https://aka.ms/rhui-update).
* Saiba mais sobre a [oferta RHEL BYOS.](./byos.md)
* As informações sobre as políticas de suporte do Chapéu Vermelho para todas as versões do RHEL podem ser encontradas na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
