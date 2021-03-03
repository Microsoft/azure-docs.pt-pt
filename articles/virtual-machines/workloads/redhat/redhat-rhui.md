---
title: Infraestrutura de atualização de chapéu vermelho | Microsoft Docs
description: Saiba mais sobre a Infraestrutura de Atualização de Chapéu Vermelho para instâncias on-demand Red Hat Enterprise Linux no Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 968377ed09996b9a717e0739a3de8355d1c8d88d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677143"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização de chapéu vermelho para red hat enterprise Linux VMs em Azure
 [A Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite que os fornecedores de nuvem, como o Azure, espelham o conteúdo do repositório apresentado pelo Chapéu Vermelho, criem repositórios personalizados com conteúdo específico do Azure e disponibilizem-no para os VMs do utilizador final.

As imagens Red Hat Enterprise Linux (RHEL) Pay-As-You-Go (PAYG) são pré-configuradas para aceder a Azure RHUI. Não é necessária nenhuma configuração adicional. Para obter as últimas atualizações, corra `sudo yum update` depois da sua instância RHEL estar pronta. Este serviço está incluído como parte das taxas de software RHEL PAYG.

Informações adicionais sobre imagens RHEL em Azure, incluindo políticas de publicação e retenção, estão disponíveis [aqui.](./redhat-images.md)

As informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

> [!IMPORTANT]
> O RHUI destina-se apenas a imagens pay-as-you-go (PAYG). Para imagens personalizadas e douradas, também conhecidas como bring-your-your-own-subscription (BYOS), o sistema precisa de ser ligado ao RHSM ou ao Satélite para receber atualizações. Consulte [o artigo da Red Hat](https://access.redhat.com/solutions/253273) para mais detalhes.


## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre Azure RHUI

* Azure RHUI é a infraestrutura de atualização que suporta todos os VMs RHEL PAYG criados em Azure. Isto não o impede de registar os seus VMs RHEL PAYG com Gestor de Assinatura ou Satélite ou outra fonte de atualizações, mas fazê-lo com um VM PAYG resultará numa faturação dupla indireta. Consulte o seguinte ponto para mais detalhes.
* O acesso ao RHUI hospedado em Azure está incluído no preço de imagem RHEL PAYG. Se não registar um PAYG RHEL VM do RHUI hospedado em Azure que não converte a máquina virtual em um tipo de VM de auto-condução própria (BYOL). Se registar o mesmo VM com outra fonte de atualizações, poderá incorrer em duplos encargos _indiretos._ É cobrado pela primeira vez pela taxa de software Azure RHEL. É cobrado pela segunda vez as assinaturas da Red Hat que foram compradas anteriormente. Se precisar de utilizar uma infraestrutura de atualização que não seja a RHUI, considere registar-se para utilizar as [imagens RHEL BYOS](./byos.md).

* As imagens RHEL SAP PAYG em Azure (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) estão ligadas a canais RHUI dedicados que permanecem na versão menor específica da RHEL, conforme exigido para a certificação SAP.

* O acesso ao RHUI hospedado no Azure está limitado aos VMs dentro dos intervalos IP do [datacenter Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se estiver a procurar todo o tráfego VM através de uma infraestrutura de rede no local, poderá ter de configurar rotas definidas pelo utilizador para que os VMs RHEL PAYG acedam ao Azure RHUI. Se for esse o caso, as rotas definidas pelo utilizador terão de ser adicionadas para _todos os_ endereços IP RHUI.


## <a name="image-update-behavior"></a>Comportamento da atualização de imagem

A partir de abril de 2019, o Azure oferece imagens RHEL que estão ligadas aos repositórios de Suporte de Atualização Alargada (EUS) por padrão e imagens RHEL que vêm ligadas aos repositórios regulares (não-EUS) por padrão. Mais detalhes sobre o EUS RHEL estão disponíveis na [documentação](https://access.redhat.com/support/policy/updates/errata) do ciclo de vida da versão da Red Hat e [documentação EUS](https://access.redhat.com/articles/rhel-eus). O comportamento padrão `sudo yum update` variará dependendo da imagem RHEL que provisa, uma vez que diferentes imagens estão ligadas a diferentes repositórios.

Para obter uma lista completa de imagens, corra `az vm image list --publisher redhat --all` utilizando o Azure CLI.

### <a name="images-connected-to-non-eus-repositories"></a>Imagens ligadas a repositórios não-EUS

Se forcar um VM a partir de uma imagem RHEL que esteja ligada a repositórios não-EUS, será atualizado para a versão mais recente da RHEL menor quando correr `sudo yum update` . Por exemplo, se forre um VM a partir de uma imagem RHEL 7.4 PAYG e `sudo yum update` executado, acaba por ter um RHEL 7.8 VM (a versão menor mais recente da família RHEL7).

As imagens ligadas a repositórios não-EUS não conterão um número de versão menor no SKU. O SKU é o terceiro elemento na URN (nome completo da imagem). Por exemplo, todas as seguintes imagens são anexadas a repositórios não-EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Note que os SKUs são 7-LVM ou 7-RAW. A versão menor é indicada na versão (quarto elemento na URN) destas imagens.

### <a name="images-connected-to-eus-repositories"></a>Imagens ligadas aos repositórios do EUS

Se forres um VM a partir de uma imagem RHEL que esteja ligada aos repositórios EUS, não será atualizado para a versão mais recente da RHEL menor quando estiver esgotado `sudo yum update` . Isto porque as imagens ligadas aos repositórios eus também estão bloqueadas na versão mais pequena.

As imagens ligadas aos repositórios eus conterão um número de versão menor no SKU. Por exemplo, todas as seguintes imagens são anexadas aos repositórios da EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS e VMs RHEL de bloqueio de versões

Os repositórios de suporte de atualização alargada (EUS) estão disponíveis para clientes que possam querer bloquear os seus VMs RHEL a uma determinada versão menor da RHEL após a disponibilização do VM. Pode bloquear a versão do seu RHEL VM numa versão menor específica, atualizando os repositórios para apontar para os repositórios de Suporte de Atualização Alargada. Também pode desfazer a operação de bloqueio de versão EUS.

>[!NOTE]
> A EUS não é apoiada em extras RHEL. Isto significa que, se estiver a instalar um pacote que normalmente está disponível no canal RHEL Extras, não poderá fazê-lo durante a UE. O ciclo de vida do produto red hat extras é detalhado [aqui.](https://access.redhat.com/support/policy/updates/extras/)

No momento desta redação, o apoio da UE terminou para a <DAR = 7.4. Consulte a secção "Red Hat Enterprise Linux Extended Maintenance" na [documentação](https://access.redhat.com/support/policy/updates/errata/#Long_Support) do Chapéu Vermelho para obter mais detalhes.
* Apoio DA REL 7.4 EUS termina a 31 de agosto de 2019
* Apoio DA REL 7.5 EUS termina a 30 de abril de 2020
* Apoio DA REL 7.6 EUS termina em 31 de maio de 2021
* Apoio DA REL 7.7 EUS termina a 30 de agosto de 2021

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>Mude um RHEL VM 7.x para EUS (versão-lock para uma versão menor específica)
Utilize as seguintes instruções para bloquear um REL 7.x VM a uma libertação menor (executar como raiz):

>[!NOTE]
> Isto aplica-se apenas às versões RHEL 7.x para as quais a EUS está disponível. No momento desta escrita, isto inclui RHEL 7.2-7.7. Mais detalhes estão disponíveis na página [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

1. Desativar os repos não comunitários:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adicionar repos EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloqueie a `releasever` variável (corra como raiz):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueada bloqueia a libertação ligeira do RHEL para a libertação de menores atuais. Introduza uma versão menor específica se estiver a tentar atualizar e bloquear para uma versão posterior que não seja a mais recente. Por exemplo, `echo 7.5 > /etc/yum/vars/releasever` bloqueará a sua versão RHEL para RHEL 7.5.

1. Atualize o seu RHEL VM
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>Mude um RHEL VM 8.x para EUS (versão-lock para uma versão menor específica)
Utilize as seguintes instruções para bloquear um REL 8.x VM a uma determinada libertação menor (executar como raiz):

>[!NOTE]
> Isto aplica-se apenas às versões RHEL 8.x para as quais a EUS está disponível. No momento desta escrita, isto inclui RHEL 8.1-8.2. Mais detalhes estão disponíveis na página [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)

1. Desativar os repos não comunitários:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. Obtenha o ficheiro deconfig de repousos EUS:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. Adicionar repos EUS:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. Bloqueie a `releasever` variável (corra como raiz):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueada bloqueia a libertação ligeira do RHEL para a libertação de menores atuais. Introduza uma versão menor específica se estiver a tentar atualizar e bloquear para uma versão posterior que não seja a mais recente. Por exemplo, `echo 8.1 > /etc/yum/vars/releasever` bloqueará a sua versão RHEL para RHEL 8.1.

    >[!NOTE]
    > Se houver problemas de permissão para aceder ao releasever, pode editar o ficheiro utilizando 'nano/etc/yum/vars/releaseve' e adicionar os detalhes da versão de imagem e guardar ('Ctrl+o' então prima entrar e depois 'Ctrl+x').  

1. Atualize o seu RHEL VM
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>Mude um RHEL 7.x VM de volta para não-EUS (remover um bloqueio de versão)
Executar o seguinte como raiz:
1. Remova o `releasever` ficheiro:
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

1. Atualize o seu RHEL VM
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>Mude um RHEL 8.x VM de volta para não-EUS (remover um bloqueio de versão)
Executar o seguinte como raiz:
1. Remova o `releasever` ficheiro:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Desativar os repos da UE:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. Obtenha o ficheiro regular deconfig repos:
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. Adicionar repos EUS:
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
    ```
    
1. Atualize o seu RHEL VM
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de entrega de conteúdo RHUI

O RHUI está disponível em todas as regiões onde as imagens rhel on-demand estão disponíveis. Atualmente inclui todas as regiões públicas listadas na página do painel de instrumentos de [estado Azure,](https://azure.microsoft.com/status/) governo Azure EUA e regiões da Microsoft Azure Germany.

Se estiver a utilizar uma configuração de rede para restringir ainda mais o acesso a partir de VMs RHEL PAYG, certifique-se de que os seguintes IPs são autorizados `yum update` a funcionar dependendo do ambiente em que se encontram:


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
>[!NOTE]
>As novas imagens do Governo dos EUA, a partir de janeiro de 2020, vão utilizar IP público mencionado no cabeçalho da Azure Global acima.

>[!NOTE]
>Note-se também que a Alemanha Azure é depreciada a favor das regiões públicas da Alemanha. A recomendação para os clientes da Azure Germany é começar a apontar para o RHUI público usando os passos [aqui.](#manual-update-procedure-to-use-the-azure-rhui-servers)

## <a name="azure-rhui-infrastructure"></a>Infraestrutura Azure RHUI


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualização expirada certificado de cliente RHUI em um VM

Se estiver a utilizar uma imagem RHEL VM mais antiga, por exemplo, RHEL 7.4 (URN de imagem: `RedHat:RHEL:7.4:7.4.2018010506` ), irá sentir problemas de conectividade com o RHUI devido a um certificado de cliente TLS/SSL agora expirado. O erro que vê pode parecer _"SSL peer rejeitou o seu certificado como expirado"_ ou _"Erro: Não é possível recuperar metadados de repositório (repomd.xml) para repositório: ... Por favor, verifique o seu caminho e tente novamente"_. Para ultrapassar este problema, por favor atualize o pacote de clientes RHUI no VM utilizando o seguinte comando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Em alternativa, o funcionamento `sudo yum update` também pode atualizar o pacote de certificados do cliente (dependendo da sua versão RHEL), apesar dos erros de "certificado SSL expirado" que verá para outros repositórios. Se esta atualização for bem sucedida, a conectividade normal com outros repositórios RHUI deve ser restaurada, para que possa executar `sudo yum update` com sucesso.

Se encontrar um erro de 404 durante a execução de `yum update` um, tente o seguinte para refrescar a sua cache yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Problemas de ligação à Azure RHUI
Se sentir problemas de ligação ao Azure RHUI a partir do seu Azure RHEL PAYG VM, siga estes passos:

1. Inspecione a configuração VM para o ponto final Azure RHUI:

    1. Verifique se o `/etc/yum.repos.d/rh-cloud.repo` ficheiro contém uma referência à secção do `rhui-[1-3].microsoft.com` `baseurl` `[rhui-microsoft-azure-rhel*]` ficheiro. Se acontecer, estás a usar o novo Azure RHUI.

    1. Se aponta para um local com o seguinte padrão, `mirrorlist.*cds[1-4].cloudapp.net` é necessária uma atualização de configuração. Estás a usar a foto vm antiga, e precisas de atualizá-la para apontar para o novo Azure RHUI.

1. O acesso ao RHUI hospedado no Azure está limitado a VMs dentro dos intervalos IP do [datacenter Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Se estiver a utilizar a nova configuração, verificou que o VM se conecta a partir da gama IP Azure, e ainda não consegue ligar-se ao Azure RHUI, arquivar um caso de suporte com a Microsoft ou o Red Hat.

### <a name="infrastructure-update"></a>Atualização da infraestrutura

Em setembro de 2016, implementamos um Azure RHUI atualizado. Em abril de 2017, fechámos o velho Azure RHUI. Se tiver usado as imagens RHEL PAYG (ou as suas fotos) a partir de setembro de 2016 ou posteriormente, está automaticamente a ligar-se ao novo Azure RHUI. Se, no entanto, tiver imagens mais antigas nos seus VMs, tem de atualizar manualmente a sua configuração para aceder ao Azure RHUI, conforme descrito numa secção a seguir.

Os novos servidores Azure RHUI são implantados com [o Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/). No Traffic Manager, um único ponto final (rhui-1.microsoft.com) pode ser utilizado por qualquer VM, independentemente da região.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para utilizar os servidores Azure RHUI
Este procedimento está previsto apenas para referência. As imagens RHEL PAYG já têm a configuração correta para ligar ao Azure RHUI. Para atualizar manualmente a configuração para utilizar os servidores Azure RHUI, complete os seguintes passos:

- Para RHEL 6:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- Para RHEL 7:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

- Para RHEL 8:
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
* Para criar um Red Hat Enterprise Linux VM a partir de uma imagem Azure Marketplace PAYG e para usar o RHUI hospedado no Azure, vá ao [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6).
* Para saber mais sobre as imagens do Chapéu Vermelho em Azure, aceda à página de [documentação.](./redhat-images.md)
* As informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [Red Hat Enterprise Linux Life Cycle.](https://access.redhat.com/support/policy/updates/errata)
