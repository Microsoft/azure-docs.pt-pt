---
title: Infraestrutura de atualização do Red Hat | Documentos da Microsoft
description: Saiba mais sobre a infraestrutura de atualização do Red Hat para instâncias de Red Hat Enterprise Linux a pedido no Microsoft Azure
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
ms.openlocfilehash: dc4762cbda5ad2877d2d69953d2514dea17c8b46
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368895"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização do Red Hat a pedido Red Hat Enterprise para VMs do Linux no Azure
 [A Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite que os fornecedores de nuvem, como o Azure, espelham conteúdos repositórios hospedados em Red Hat, criem repositórios personalizados com conteúdo específico do Azure e disponibilizem-no aos VMs de utilizador final.

Imagens de pay as you go (PAYG) da Red Hat Enterprise Linux (RHEL) vêm pré-configurados para aceder a RHUI do Azure. Não é necessária nenhuma configuração adicional. Para obter as últimas atualizações, faça `sudo yum update` depois da sua instância RHEL estar pronta. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

Informações adicionais sobre imagens RHEL em Azure, incluindo políticas de publicação e retenção, estão disponíveis [aqui.](./redhat-images.md)

As informações sobre as políticas de suporte do Chapéu Vermelho para todas as versões do RHEL podem ser encontradas na página [red hat enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

> [!IMPORTANT]
> RHUI destina-se apenas a imagens pay-as-you-go (PAYGO). Para imagens personalizadas e douradas, também conhecidas como bring-your-your-own-subscrição (BYOS), o sistema precisa de ser ligado ao RHSM ou satélite para receber atualizações. Consulte o [artigo do Red Hat](https://access.redhat.com/solutions/253273) para mais detalhes.


## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o Azure RHUI

* Azure RHUI é a infraestrutura de atualização que suporta todos os VMs RHEL PAYG criados em Azure. Isto não o impede de registar os seus VMs PAYG RHEL com O Gestor de Assinaturaou Satélite ou outra fonte de atualizações, mas fazê-lo com um VM PAYG resultará em faturação dupla indireta. Consulte o seguinte ponto para mais detalhes.
* Acesso para o RHUI alojado no Azure está incluído no preço de imagem RHEL PAYG. Se cancelar o registro de uma VM do RHEL PAYG a partir do RHUI alojado no Azure que não converter a máquina virtual num tipo bring-your-own-license (BYOL) da VM. Se registar o mesmo VM com outra fonte de atualizações, poderá incorrer em duplas taxas _indiretas._ Será cobrado pela primeira vez para a taxa de software RHEL do Azure. Será cobrado na segunda vez para subscrições do Red Hat que foram adquiridas anteriormente. Se precisar de utilizar uma infraestrutura de atualização diferente da RHUI hospedada no Azure, considere registar-se para utilizar as [imagens RHEL BYOS](./byos.md).

* As imagens RHEL SAP PAYG em Azure (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) estão ligadas a canais RHUI dedicados que permanecem na versão rheL específica, conforme necessário para a certificação SAP.

* O acesso ao RHUI hospedado no Azure está limitado aos VMs dentro das gamas IP do centro de [dados Azure.](https://www.microsoft.com/download/details.aspx?id=41653) Se estiver a utilização do proxy todo o tráfego de VM por meio de uma infraestrutura de rede no local, poderá ter de configurar rotas definidas pelo utilizador para as VMs de PAYG RHEL acessar o RHUI do Azure. Se for esse o caso, as rotas definidas pelo utilizador terão de ser adicionadas para _todos os_ endereços IP rHUI.


## <a name="image-update-behavior"></a>Comportamento de atualização de imagem

A partir de abril de 2019, o Azure oferece imagens RHEL que estão ligadas aos repositórios de Suporte de Atualização Estendida (EUS) por padrão e imagens RHEL que vêm ligadas aos repositórios regulares (não-EUS) por padrão. Mais detalhes sobre o ReHEL EUS estão disponíveis na [documentação](https://access.redhat.com/support/policy/updates/errata) do ciclo de vida da versão da Red Hat e na [documentação do EUS.](https://access.redhat.com/articles/rhel-eus) O comportamento padrão de `sudo yum update` variará dependendo da imagem RHEL de que você disponibilizou, uma vez que diferentes imagens estão ligadas a diferentes repositórios.

Para obter uma lista completa de imagens, execute `az vm image list --publisher redhat --all` utilizando o Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Imagens ligadas a repositórios não-EUS

Se fornecer um VM a partir de uma imagem RHEL que esteja ligada a repositórios não-EUS, será atualizado para a versão menor rHEL mais recente quando executar `sudo yum update`. Por exemplo, se fornecer um VM a partir de uma imagem RHEL 7.4 PAYG e executar `sudo yum update`, você acaba com um RHEL 7.7 VM (a versão menor mais recente na família RHEL7).

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

Se fornecer um VM a partir de uma imagem RHEL que esteja ligada aos repositórios eus, não será atualizado para a versão menor rHEL mais recente quando executar `sudo yum update`. Isto porque as imagens ligadas aos repositórios da EUS também estão bloqueadas pela sua versão menor específica.

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

No momento desta escrita, o apoio da EUS terminou para rHEL <= 7,4. Consulte a secção "Red Hat Enterprise Linux Longer Support Add-Ons" na [documentação](https://access.redhat.com/support/policy/updates/errata/) do Chapéu Vermelho para obter mais detalhes.
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

1. Bloqueie a variável do releasever (executar como raiz):
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
1. Remova o ficheiro do releasever:
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

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de entrega de conteúdos do RHUI

RHUI está disponível em todas as regiões onde as imagens de sob demanda do RHEL estão disponíveis. Atualmente inclui todas as regiões públicas listadas na página do painel de instrumentos de estado do [Azure,](https://azure.microsoft.com/status/) governo azure dos EUA e regiões da Microsoft Azure Germany.

Se estiver a utilizar uma configuração de rede para restringir ainda mais o acesso dos VMs RHEL PAYG, certifique-se de que os seguintes IPs são permitidos para `yum update` funcionar em função do ambiente em que se encontre:


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


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualizar o certificado de cliente do RHUI expirado numa VM

Se estiver a utilizar uma imagem RHEL VM mais antiga, por exemplo, RHEL 7.4 (imagem URN: `RedHat:RHEL:7.4:7.4.2018010506`), irá experimentar problemas de conectividade com a RHUI devido a um certificado de cliente SSL agora expirado. O erro que vê pode parecer _"SSL peer rejeitou o seu certificado como expirado"_ ou _"Error: Can Can retrieve metadados repositórios (repomd.xml) para repositório: ... Por favor, verifique o seu caminho e tente novamente"._ Para ultrapassar este problema, por favor atualize o pacote de cliente RHUI no VM utilizando o seguinte comando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Em alternativa, a execução `sudo yum update` também pode atualizar o pacote de certificados de cliente (dependendo da sua versão RHEL), apesar dos erros de "certificado SSL expirado" que irá ver para outros repositórios. Se esta atualização for bem sucedida, a conectividade normal com outros repositórios RHUI deve ser restaurada, para que possa correr `sudo yum update` com sucesso.

Se tiver um erro de 404 durante a execução de um `yum update`, tente o seguinte para refrescar a sua cache de yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Resolver problemas de ligação para o Azure RHUI
Se tiver problemas a ligar ao Azure RHUI da sua VM do Azure RHEL PAYG, siga estes passos:

1. Verifique a configuração de VM para o ponto de final do RHUI do Azure:

    1. Verifique se o ficheiro `/etc/yum.repos.d/rh-cloud.repo` contém uma referência a `rhui-[1-3].microsoft.com` na `baseurl` da secção `[rhui-microsoft-azure-rhel*]` do ficheiro. Se assim for, está usando o novo RHUI do Azure.

    1. Se apontar para um local com o seguinte padrão, `mirrorlist.*cds[1-4].cloudapp.net`, é necessária uma atualização de configuração. Estiver a utilizar o antigo instantâneo VM e terá de atualizá-lo para apontar para o novo RHUI do Azure.

1. O acesso ao RHUI hospedado no Azure está limitado a VMs dentro das gamas IP do centro de [dados Azure.](https://www.microsoft.com/download/details.aspx?id=41653)

1. Se estiver a utilizar a nova configuração, ter verificado que a VM liga-se de que o intervalo de IP do Azure e ainda não é possível ligar ao Azure RHUI, o ficheiro de um incidente de suporte com a Microsoft ou do Red Hat.

### <a name="infrastructure-update"></a>Atualização de infraestruturas

Em Setembro de 2016, Implementámos uma RHUI atualizados do Azure. Em Abril de 2017, vamos encerrar o antigo RHUI do Azure. Se usa as imagens RHEL PAYG (ou respetivos instantâneos) de Setembro de 2016 ou posterior, está a ligar automaticamente para o novo RHUI do Azure. Se, no entanto, tiver instantâneos mais antigos nas suas VMs, terá de atualizar manualmente a configuração para aceder a RHUI de Azure conforme descrito na seção a seguir.

Os novos servidores Azure RHUI são implantados com o [Azure Traffic Manager.](https://azure.microsoft.com/services/traffic-manager/) No Gestor de tráfego, um único ponto final (rhui 1.microsoft.com) pode ser utilizado por qualquer VM, independentemente da região.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para utilizar os servidores do RHUI do Azure
Este procedimento é fornecido apenas para referência. Imagens RHEL PAYG já tem a configuração correta para se ligar ao Azure RHUI. Para atualizar manualmente a configuração para utilizar os servidores do RHUI do Azure, conclua os seguintes passos:

- Para RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Para RHEL 7:
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
