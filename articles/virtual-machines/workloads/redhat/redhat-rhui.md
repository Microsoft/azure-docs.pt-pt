---
title: Infraestrutura de Atualização do Chapéu Vermelho Microsoft Docs
description: Saiba mais sobre a Infraestrutura de Atualização do Chapéu Vermelho para os casos de Red Hat Enterprise Linux a pedido no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: aa9fd230f59b5e46576e78beb0436c85449d3c5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256917"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização de chapéu vermelho para Red Hat Enterprise Linux VMs em Azure
 [A Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite que os fornecedores de nuvem, como o Azure, espelham conteúdos repositórios hospedados em Red Hat, criem repositórios personalizados com conteúdo específico do Azure e disponibilizem-no aos VMs de utilizador final.

As imagens Pay-As-You-Go (PAYG) da Red Hat Enterprise (RHEL) são pré-configuradas para aceder ao Azure RHUI. Não é necessária nenhuma configuração adicional. Para obter as últimas `sudo yum update` atualizações, corra depois da sua instância RHEL estar pronta. Este serviço está incluído como parte das taxas de software RHEL PAYG.

Informações adicionais sobre imagens RHEL em Azure, incluindo políticas de publicação e retenção, estão disponíveis [aqui.](./redhat-images.md)

As informações sobre as políticas de suporte do Chapéu Vermelho para todas as versões do RHEL podem ser encontradas na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

> [!IMPORTANT]
> RHUI destina-se apenas a imagens pay-as-you-go (PAYG). Para imagens personalizadas e douradas, também conhecidas como bring-your-your-own-subscrição (BYOS), o sistema precisa de ser ligado ao RHSM ou satélite para receber atualizações. Consulte o [artigo do Red Hat](https://access.redhat.com/solutions/253273) para mais detalhes.


## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre Azure RHUI

* Azure RHUI é a infraestrutura de atualização que suporta todos os VMs RHEL PAYG criados em Azure. Isto não o impede de registar os seus VMs PAYG RHEL com O Gestor de Assinaturaou Satélite ou outra fonte de atualizações, mas fazê-lo com um VM PAYG resultará em faturação dupla indireta. Consulte o seguinte ponto para mais detalhes.
* O acesso ao RHUI hospedado no Azure está incluído no preço de imagem RHEL PAYG. Se não registar um VM PAYG RHEL do RHUI hospedado em Azure que não converta a máquina virtual num tipo de VM de porta-a-própria (BYOL). Se registar o mesmo VM com outra fonte de atualizações, poderá incorrer em duplas taxas _indiretas._ É cobrado pela primeira vez pela taxa de software Azure RHEL. É cobrado pela segunda vez pelas assinaturas do Red Hat que foram compradas anteriormente. Se precisar de utilizar uma infraestrutura de atualização diferente da RHUI hospedada no Azure, considere registar-se para utilizar as [imagens RHEL BYOS](./byos.md).

* As imagens RHEL SAP PAYG em Azure (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) estão ligadas a canais RHUI dedicados que permanecem na versão rheL específica, conforme necessário para a certificação SAP.

* O acesso ao RHUI hospedado no Azure está limitado aos VMs dentro das gamas IP do centro de [dados Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Se estiver a procurar todo o tráfego vm através de uma infraestrutura de rede no local, poderá ter de configurar rotas definidas pelo utilizador para que os VMs RHEL PAYG acedam ao Azure RHUI. Se for esse o caso, as rotas definidas pelo utilizador terão de ser adicionadas para _todos os_ endereços IP rHUI.


## <a name="image-update-behavior"></a>Comportamento de atualização de imagem

A partir de abril de 2019, o Azure oferece imagens RHEL que estão ligadas aos repositórios de Suporte de Atualização Estendida (EUS) por padrão e imagens RHEL que vêm ligadas aos repositórios regulares (não-EUS) por padrão. Mais detalhes sobre o ReHEL EUS estão disponíveis na [documentação](https://access.redhat.com/support/policy/updates/errata) do ciclo de vida da versão da Red Hat e na [documentação do EUS.](https://access.redhat.com/articles/rhel-eus) O comportamento `sudo yum update` padrão da vontade variará dependendo da imagem RHEL de que você disponibilizou, uma vez que diferentes imagens estão ligadas a diferentes repositórios.

Para obter uma lista `az vm image list --publisher redhat --all` completa de imagens, corra utilizando o Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Imagens ligadas a repositórios não-EUS

Se fornecer um VM a partir de uma imagem RHEL que esteja ligada a repositórios não-EUS, será atualizado para a versão menor rHEL mais recente quando executar `sudo yum update`. Por exemplo, se fornecer um VM a partir de uma `sudo yum update`imagem RHEL 7.4 PAYG e executar, você acaba com um RHEL 7.7 VM (a versão menor mais recente na família RHEL7).

As imagens ligadas a repositórios não comunitários não conterão um número de versão menor no SKU. O SKU é o terceiro elemento da URN (nome completo da imagem). Por exemplo, todas as seguintes imagens são anexadas a repositórios não-EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Note que as SKUs são 7-LVM ou 7-RAW. A versão menor é indicada na versão (quarto elemento na URN) destas imagens.

### <a name="images-connected-to-eus-repositories"></a>Imagens ligadas aos repositórios da EUS

Se fornecer um VM a partir de uma imagem RHEL que esteja ligada aos repositórios eus, `sudo yum update`não será atualizado para a versão menor rHEL mais recente quando executar . Isto porque as imagens ligadas aos repositórios da EUS também estão bloqueadas pela sua versão menor específica.

As imagens ligadas aos repositórios do EUS conterão um número de versão menor no SKU. Por exemplo, todas as seguintes imagens vêm anexadas aos repositórios da EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS e VMs rhel de bloqueio de versão

Os repositórios de Suporte a Atualização Alargada (EUS) estão disponíveis para os clientes que possam querer bloquear os seus VMs RHEL a uma certa libertação menor rHEL após o fornecimento do VM. Pode bloquear o seu VM RHEL numa versão menor específica, atualizando os repositórios para apontar para os repositórios de Suporte de Atualização Estendida. Também pode desfazer a operação de bloqueio de versão EUS.

>[!NOTE]
> A EUS não é apoiada em Extras RHEL. Isto significa que, se estiver a instalar um pacote que normalmente está disponível no canal RHEL Extras, não poderá fazê-lo enquanto estiver no EUS. O Ciclo de Vida do Produto Red Hat Extras é detalhado [aqui.](https://access.redhat.com/support/policy/updates/extras/)

No momento desta escrita, o apoio da UES terminou para a RHEL <= 7,4. Consulte a secção "Red Hat Enterprise Linux Longer Support Add-Ons" na [documentação](https://access.redhat.com/support/policy/updates/errata/) do Chapéu Vermelho para obter mais detalhes.
* Apoio ao RHEL 7.4 EUS termina em 31 de agosto de 2019
* Apoio ao RHEL 7.5 EUS termina em 30 de abril de 2020
* Apoio do RHEL 7.6 EUS termina a 31 de outubro de 2020
* Apoio ao RHEL 7.7 EUS termina em 30 de agosto de 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Mude um VM RHEL para EUS (versão-lock para uma versão menor específica)
Utilize as seguintes instruções para fixar um VM RHEL a uma determinada libertação menor (executar como raiz):

>[!NOTE]
> Isto aplica-se apenas às versões RHEL para as quais o EUS está disponível. No momento desta escrita, isto inclui RHEL 7.2-7.7. Mais detalhes estão disponíveis na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

1. Desativar os repos não COMUNITÁRIOS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adicione repos eus:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloqueie `releasever` a variável (correr como raiz):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueia a libertação menor rHEL à libertação menor atual. Introduza uma versão menor específica se estiver a tentar atualizar e bloquear uma libertação menor posterior que não seja a mais recente. Por exemplo, `echo 7.5 > /etc/yum/vars/releasever` bloqueará a sua versão RHEL para RHEL 7.5

1. Atualize o seu VM RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Mude um VM RHEL de volta para não-EUS (remova um bloqueio de versão)
Executar o seguinte como raiz:
1. Remova `releasever` o ficheiro:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Desativar os repos da UE:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Configure RHEL VM
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. Atualize o seu VM RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de entrega de conteúdo RHUI

RHUI está disponível em todas as regiões onde as imagens RHEL on-demand estão disponíveis. Atualmente inclui todas as regiões públicas listadas na página do painel de instrumentos de estado do [Azure,](https://azure.microsoft.com/status/) governo azure dos EUA e regiões da Microsoft Azure Germany.

Se estiver a utilizar uma configuração de rede para restringir ainda mais o acesso dos `yum update` VMs RHEL PAYG, certifique-se de que os seguintes IPs são autorizados a funcionar dependendo do ambiente em que se encontram:


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infraestrutura Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualização certificado de cliente RHUI expirado em um VM

Se estiver a utilizar uma imagem De VM RHEL mais antiga, por `RedHat:RHEL:7.4:7.4.2018010506`exemplo, RHEL 7.4 (imagem URN: ), irá experimentar problemas de conectividade com a RHUI devido a um certificado de cliente TLS/SSL já expirado. O erro que vê pode parecer _"SSL peer rejeitou o seu certificado como expirado"_ ou _"Error: Can Can retrieve metadados repositórios (repomd.xml) para repositório: ... Por favor, verifique o seu caminho e tente novamente"._ Para ultrapassar este problema, por favor atualize o pacote de cliente RHUI no VM utilizando o seguinte comando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Em alternativa, `sudo yum update` a execução também pode atualizar o pacote de certificado de cliente (dependendo da sua versão RHEL), apesar dos erros de "certificado SSL expirado" que irá ver para outros repositórios. Se esta atualização for bem sucedida, a conectividade normal com outros repositórios `sudo yum update` RHUI deve ser restaurada, para que possa funcionar com sucesso.

Se tiver um erro de 404 durante a execução, `yum update`tente o seguinte para refrescar a sua cache de yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Problemas de ligação a Azure RHUI
Se tiver problemas de ligação ao Azure RHUI a partir do seu Azure RHEL PAYG VM, siga estes passos:

1. Inspecione a configuração VM para obter o ponto final do Azure RHUI:

    1. Verifique se `/etc/yum.repos.d/rh-cloud.repo` o ficheiro `rhui-[1-3].microsoft.com` contém `baseurl` uma `[rhui-microsoft-azure-rhel*]` referência na secção do ficheiro. Se acontecer, estás a usar o novo Azure RHUI.

    1. Se apontar para um local com `mirrorlist.*cds[1-4].cloudapp.net`o seguinte padrão, é necessária uma atualização de configuração. Estás a usar o velho instantâneo vm, e precisas de atualizá-lo para apontar para o novo Azure RHUI.

1. O acesso ao RHUI hospedado no Azure está limitado a VMs dentro das gamas IP do centro de [dados Azure.](https://www.microsoft.com/download/details.aspx?id=41653)

1. Se estiver a utilizar a nova configuração, verificou que o VM se liga a partir da gama IP Do Azure e ainda não consegue ligar-se ao Azure RHUI, arquiva um caso de suporte com a Microsoft ou o Red Hat.

### <a name="infrastructure-update"></a>Atualização de infraestruturas

Em setembro de 2016, implementámos um Azure RHUI atualizado. Em abril de 2017, fechámos o velho Azure RHUI. Se tiver usado as imagens RHEL PAYG (ou as suas fotos) a partir de setembro de 2016 ou mais tarde, está automaticamente ligado ao novo Azure RHUI. No entanto, se tiver fotografias mais antigas nos seus VMs, precisa de atualizar manualmente a sua configuração para aceder ao Azure RHUI, conforme descrito numa secção seguinte.

Os novos servidores Azure RHUI são implantados com o [Azure Traffic Manager.](https://azure.microsoft.com/services/traffic-manager/) No Traffic Manager, um único ponto final (rhui-1.microsoft.com) pode ser utilizado por qualquer VM, independentemente da região.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para utilizar os servidores Azure RHUI
Este procedimento está previsto apenas para referência. As imagens RHEL PAYG já têm a configuração correta para ligar ao Azure RHUI. Para atualizar manualmente a configuração para utilizar os servidores Azure RHUI, complete os seguintes passos:

- Para o RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Para rhel 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Para rhel 8:
    1. Criar um ficheiro config:
        ```bash
        vi rhel8.config
        ```
    1. Adicione o seguinte conteúdo no ficheiro config:
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. Guarde o ficheiro e execute o seguinte comando:
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. Atualize o seu VM
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>Passos seguintes
* Para criar um Red Hat Enterprise Linux VM a partir de uma imagem PAYG do Azure Marketplace e utilizar o RHUI hospedado em Azure, vá ao [Azure Marketplace.](https://azure.microsoft.com/marketplace/partners/redhat/)
* Para saber mais sobre as imagens do Chapéu Vermelho em Azure, vá à página de [documentação.](./redhat-images.md)
* As informações sobre as políticas de suporte do Chapéu Vermelho para todas as versões do RHEL podem ser encontradas na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
