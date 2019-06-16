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
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: b40f62a90dbe7c822b95476abe6ec25cf3fb21d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67070038"
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
Todas as imagens RHEL atualmente publicadas utilizam o modelo de pay as you go e estão ligadas ao [Red Hat atualização de infra-estrutura (RHUI) no Azure](https://aka.ms/rhui-update). Uma nova Convenção de nomenclatura foi adotada para imagens RHEL 7 de famílias em que a criação de partições de disco esquema (não processados, LVM) está especificado no SKU em vez da versão. A versão da imagem RHEL irá conter qualquer um em bruto de 7 ou 7 LVM. A nomenclatura do família de RHEL 6 não foram alterada neste momento.

Haverá 2 tipos de imagem RHEL 7 SKUs esta Convenção de nomenclatura: SKUs que listam a versão secundária e os SKUs que não. Se pretende utilizar um 7 BRUTOS ou 7-LVM SKU, pode especificar a versão secundária do RHEL que pretende implementar na versão. Se escolher a versão "mais recente", será aprovisionada a última versão secundária do RHEL.

>[!NOTE]
> No RHEL para o conjunto SAP de imagens, a versão RHEL permanece fixa. Assim, sua Convenção de nomenclatura inclui uma versão específica no SKU.

>[!NOTE]
> RHEL 6 conjunto de imagens não foram movidas para a nova Convenção de nomenclatura.

## <a name="extended-update-support-eus"></a>Suporte de atualização expandida (EUS)
Como de 2019 de Abril, as imagens RHEL estão disponíveis que estão ligados aos repositórios de suporte estendido de atualização (EUS) por predefinição. Estão disponíveis em mais detalhes no RHEL EUS [documentação de Red Hat](https://access.redhat.com/articles/rhel-eus).

Estão disponíveis instruções sobre como mudar a sua VM para EUS e mais detalhes sobre as datas de fim de vida do suporte EUS [aqui](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms).

>[!NOTE]
> EUS não é suportada no RHEL Extras. Isso significa que se estiver a instalar um pacote que está geralmente disponível do canal RHEL Extras, não será capaz de fazer isso em EUS. O ciclo de vida de produto do Red Hat Extras é detalhado [aqui](https://access.redhat.com/support/policy/updates/extras/).

### <a name="for-customers-that-want-to-use-eus-images"></a>Para os clientes que queiram utilizar imagens EUS:
Os clientes que pretendem utilizar imagens que estão anexadas a repositórios EUS devem utilizar a imagem RHEL que contém um número de versão secundária do RHEL no SKU. Estas imagens serão particionados não processados (ou seja, não LVM).

Por exemplo, poderá ver as seguintes 2 RHEL 7.4 imagens disponíveis:
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
Neste caso, `RedHat:RHEL:7.4:7.4.2019041718` será anexado a repositórios EUS por predefinição, e `RedHat:RHEL:7-RAW:7.4.2018010506` será anexado a repositórios de não-EUS por predefinição.

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>Para os clientes que não querem utilizar imagens EUS:
Se não pretender utilizar uma imagem que está ligada ao EUS por predefinição, a implementar através de uma imagem que não contém um número de versão secundária no SKU.

#### <a name="rhel-images-with-eus"></a>Imagens RHEL com EUS
A tabela seguinte se aplicará para imagens RHEL que contêm uma versão secundária no SKU.

>[!NOTE]
> No momento da escrita, apenas RHEL 7.4 e mais tarde a versões secundárias têm EUS suportar. EUS já não é suportada para RHEL < = 7.3.

Versão secundária |Exemplo de imagem EUS              |Estado EUS                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | Imagens publicadas de 2019 de Abril e mais tarde será EUS por predefinição|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | Imagens publicadas de 2019 de Junho e mais tarde será EUS por predefinição |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | Imagens publicadas Maio de 2019 e mais tarde será EUS por predefinição  |
RHEL 8.0      |N/A                            | Não existem EUS atualmente imagens atualmente disponíveis                 |


## <a name="list-of-rhel-images-available"></a>Lista de imagens RHEL disponíveis
As seguintes ofertas são que SKUs estão atualmente disponíveis para utilização geral:

Oferta| SKU | Criação de partições | Aprovisionamento | Notas
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Agente Linux | RHEL 7, a família de imagens. <br> Não ligado a repositórios EUS por predefinição.
|             | 7-LVM    | LVM    | Agente Linux | RHEL 7, a família de imagens. <br> Não ligado a repositórios EUS por predefinição.
|             | 7-RAW-CI | RAW-CI | Inicialização da cloud  | RHEL 7, a família de imagens. <br> Não ligado a repositórios EUS por predefinição.
|             | 6.7      | RAW    | Agente Linux | Imagens RHEL 6.7, convenção de nomenclatura antiga
|             | 6.8      | RAW    | Agente Linux | Mesmo como anteriormente para RHEL 6.8
|             | 6.9      | RAW    | Agente Linux | Mesmo como anteriormente para RHEL 6.9
|             | 6.10     | RAW    | Agente Linux | Mesmo como anteriormente para RHEL 6.10
|             | 7.2      | RAW    | Agente Linux | Mesmo como anteriormente para RHEL 7.2
|             | 7.3      | RAW    | Agente Linux | Mesmo como anteriormente para RHEL 7.3
|             | 7.4      | RAW    | Agente Linux | O mesmo como anteriormente para RHEL 7.4. <br> Ligado a repositórios EUS por predefinição, a partir de Abril de 2019
|             | 7.5      | RAW    | Agente Linux | O mesmo como anteriormente para RHEL 7.5. <br> Ligado a repositórios EUS por predefinição, a partir de Junho de 2019
|             | 7.6      | RAW    | Agente Linux | O mesmo como anteriormente para RHEL 7,6. <br> Ligado a repositórios EUS por predefinição, a partir de Maio de 2019
RHEL SAP      | 7.4      | LVM    | Agente Linux | RHEL 7.4 para SAP HANA e aplicações empresariais
|             | 7.5      | LVM    | Agente Linux | RHEL 7.5 para o SAP HANA e aplicações empresariais
RHEL-SAP-HANA | 6.7      | RAW    | Agente Linux | RHEL 6.7 para o SAP HANA
|             | 7.2      | LVM    | Agente Linux | 7\.2 de RHEL for SAP HANA
|             | 7.3      | LVM    | Agente Linux | 7\.3 de RHEL for SAP HANA
APLICAÇÕES DE SAP RHEL | 6.8      | RAW    | Agente Linux | 6\.8 de RHEL for SAP Business Applications
|             | 7.3      | LVM    | Agente Linux | 7\.3 de RHEL for SAP Business Applications

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
