---
title: Infraestrutura de atualização do Red Hat | Documentos da Microsoft
description: Saiba mais sobre a infraestrutura de atualização do Red Hat para instâncias de Red Hat Enterprise Linux a pedido no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 2add03d68b00fb18fb4323a6acd04480b3770c1c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708410"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização do Red Hat a pedido Red Hat Enterprise para VMs do Linux no Azure
 [Infraestrutura de atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite que os fornecedores de cloud, como o Azure, para espelhar o conteúdo do repositório alojado no Red Hat, criar repositórios personalizados com específica do Azure, conteúdo e disponibilizá-lo para VMs do utilizador final.

Imagens de pay as you go (PAYG) da Red Hat Enterprise Linux (RHEL) vêm pré-configurados para aceder a RHUI do Azure. Não é necessária nenhuma configuração adicional. Para obter as atualizações mais recentes, execute `sudo yum update` depois da sua instância do RHEL está pronta. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

Estão disponíveis informações adicionais sobre imagens RHEL no Azure, incluindo a publicação e políticas de retenção [aqui](./rhel-images.md).

Podem encontrar informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL no [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o Azure RHUI
* RHUI do Azure é a infraestrutura de atualização que suporta todas as VMs de PAYG RHEL criada no Azure. Isso não impedi-lo de registar as suas VMs do RHEL PAYG com o Gestor de subscrições ou de satélite ou de outra origem de atualizações, mas fazê-lo com uma VM do PAYG irá resultar em indireta de faturação de double. Consulte o ponto seguinte para obter detalhes.
* Acesso para o RHUI alojado no Azure está incluído no preço de imagem RHEL PAYG. Se cancelar o registro de uma VM do RHEL PAYG a partir do RHUI alojado no Azure que não converter a máquina virtual num tipo bring-your-own-license (BYOL) da VM. Se se registra a mesma VM com outra origem de atualizações, pode incorrer _indireta_ duas vezes encargos. Será cobrado pela primeira vez para a taxa de software RHEL do Azure. Será cobrado na segunda vez para subscrições do Red Hat que foram adquiridas anteriormente. Se tiver consistentemente de utilizar uma infraestrutura de atualização que não seja RHUI alojado no Azure, considere a registar para utilizar o [imagens RHEL BYOS](https://aka.ms/rhel-byos).
* O comportamento padrão do RHUI é atualizar a VM de RHEL para a versão secundária mais recente ao executar `sudo yum update`.

    Por exemplo, se aprovisionar uma VM a partir de uma imagem RHEL 7.4 PAYG e execute `sudo yum update`, acaba tendo uma VM do RHEL 7,6 (a versão mais recente secundária da família de RHEL7).

    Para evitar este comportamento, pode mudar para o [canais de suporte de atualização estendido](#rhel-eus-and-version-locking-rhel-vms) ou crie sua própria imagem, conforme descrito no [criar e carregar uma máquina de virtual baseada em Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo. Se criar sua própria imagem, precisa para ligá-lo para uma infraestrutura de atualização diferentes ([diretamente ao Red Hat conteúdo servidores de entrega](https://access.redhat.com/solutions/253273) ou uma [servidor Red Hat satélite](https://access.redhat.com/products/red-hat-satellite)).



* Imagens RHEL PAYG de SAP no Azure (de RHEL for SAP, de RHEL for SAP HANA e de RHEL for SAP Business Applications) estão ligadas a canais RHUI dedicados que permanecem na versão secundária de RHEL específica conforme necessário para a certificação da SAP.

* O acesso a RHUI alojado no Azure é limitado para as VMs dentro de [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se estiver a utilização do proxy todo o tráfego de VM por meio de uma infraestrutura de rede no local, poderá ter de configurar rotas definidas pelo utilizador para as VMs de PAYG RHEL acessar o RHUI do Azure.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS e VMs do RHEL de bloqueio de versão
Alguns clientes talvez queira bloquear suas VMs do RHEL para uma versão de determinados RHEL pequenas. Pode o bloqueio de versão a VM RHEL para uma versão específica de pequenas, atualizando os repositórios para que apontem para os repositórios de suporte estendido de atualização. Também pode anular a operação de bloqueio de versão EUS.

>[!NOTE]
> EUS não é suportada no RHEL Extras. Isso significa que se estiver a instalar um pacote que está geralmente disponível do canal RHEL Extras, não será capaz de fazer isso em EUS. O ciclo de vida de produto do Red Hat Extras é detalhado [aqui](https://access.redhat.com/support/policy/updates/extras/).

No momento da redação deste artigo, o suporte EUS terminou de RHEL < = 7.3. Consulte a secção "Red Hat Enterprise Linux mais suporte suplementos" a [documentação de Red Hat](https://access.redhat.com/support/policy/updates/errata/) para obter mais detalhes.
* RHEL 7.4 EUS o suporte termina 31 de Agosto de 2019
* RHEL 7.5 EUS o suporte termina 30 de Abril de 2020
* RHEL 7,6 EUS o suporte termina 31 de Outubro de 2020

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Mudar de uma VM RHEL para EUS (bloqueio versão para uma versão específica, secundária)
Utilize as seguintes instruções para bloquear uma VM RHEL para uma versão específica de pequenas (executar como raiz):

>[!NOTE]
> Isto aplica-se apenas para versões do RHEL para o qual EUS está disponível. No momento da redação deste artigo, isto inclui o RHEL 7.2 7,6. Estão disponíveis em mais detalhes a [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.

1. Desative repositórios não EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adicione repositórios EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloquear a variável de releasever (executar como raiz):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueará a versão secundária do RHEL para a versão atual de pequenas. Introduza uma versão secundária específica, se estiver à procura a atualização e de bloqueio para a posterior pequenas versão que não é a versão mais recente. Por exemplo, `echo 7.5 > /etc/yum/vars/releasever` bloqueará a sua versão RHEL para RHEL 7.5

1. Atualizar a sua VM do RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Mudar de uma VM RHEL para não (remova um bloqueio de versão) EUS
Execute o seguinte como raiz:
1. Remova o ficheiro de releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Desative repositórios EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Atualizar a sua VM do RHEL
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de entrega de conteúdos do RHUI

RHUI está disponível em todas as regiões onde as imagens de sob demanda do RHEL estão disponíveis. Inclui todas as regiões públicas listadas atualmente o [dashboard de estado do Azure](https://azure.microsoft.com/status/) página, do Azure US Government e regiões do Microsoft Azure Alemanha.

Se estiver a utilizar uma configuração de rede para restringir mais o acesso de VMs do RHEL PAYG, certifique-se de que os seguintes IPs são permitidos para `yum update` trabalhar consoante o ambiente em que está:


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

## <a name="azure-rhui-infrastructure"></a>Infraestrutura do RHUI do Azure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualizar o certificado de cliente do RHUI expirado numa VM

Se estiver a utilizar uma imagem de RHEL VM mais antiga, por exemplo, RHEL 7.4 (imagem URN: `RedHat:RHEL:7.4:7.4.2018010506`), terá problemas de conectividade para RHUI devido a um certificado de cliente SSL expirado por agora. O erro que vê poderá ter o aspeto _"ponto a ponto do SSL rejeitou o certificado como expirada"_ ou _"erro: Não é possível obter metadados de repositório (repomd.xml) para o repositório:... Verifique o caminho e tente novamente"_ . Para superar esse problema, atualize o pacote de cliente do RHUI na VM com o seguinte comando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Em alternativa, em execução `sudo yum update` também pode atualizar o pacote de certificado de cliente (dependendo da versão RHEL), apesar de erros de "certificado SSL expirado" verá para outros repositórios. Se esta atualização for bem-sucedida, conectividade normal para outros repositórios do RHUI deve ser restaurada, por isso, será capaz de executar `sudo yum update` com êxito.

Caso se depare com um erro 404 ao ser executado um `yum update`, tente o seguinte para atualizar a cache de yum:
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Resolver problemas de ligação para o Azure RHUI
Se tiver problemas a ligar ao Azure RHUI da sua VM do Azure RHEL PAYG, siga estes passos:

1. Verifique a configuração de VM para o ponto de final do RHUI do Azure:

    1. Verifique se o `/etc/yum.repos.d/rh-cloud.repo` arquivo contém uma referência ao `rhui-[1-3].microsoft.com` no `baseurl` da `[rhui-microsoft-azure-rhel*]` secção do ficheiro. Se assim for, está usando o novo RHUI do Azure.

    1. Se ele aponta para uma localização com o seguinte padrão, `mirrorlist.*cds[1-4].cloudapp.net`, é necessária uma atualização de configuração. Estiver a utilizar o antigo instantâneo VM e terá de atualizá-lo para apontar para o novo RHUI do Azure.

1. Acesso a RHUI alojado no Azure está limitado a VMs dentro de [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Se estiver a utilizar a nova configuração, ter verificado que a VM liga-se de que o intervalo de IP do Azure e ainda não é possível ligar ao Azure RHUI, o ficheiro de um incidente de suporte com a Microsoft ou do Red Hat.

### <a name="infrastructure-update"></a>Atualização de infra-estrutura

Em Setembro de 2016, Implementámos uma RHUI atualizados do Azure. Em Abril de 2017, vamos encerrar o antigo RHUI do Azure. Se usa as imagens RHEL PAYG (ou respetivos instantâneos) de Setembro de 2016 ou posterior, está a ligar automaticamente para o novo RHUI do Azure. Se, no entanto, tiver instantâneos mais antigos nas suas VMs, terá de atualizar manualmente a configuração para aceder a RHUI de Azure conforme descrito na seção a seguir.

Os novos servidores do RHUI do Azure são implementados com [Gestor de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). No Gestor de tráfego, um único ponto final (rhui 1.microsoft.com) pode ser utilizado por qualquer VM, independentemente da região.

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

## <a name="next-steps"></a>Passos seguintes
* Para criar uma VM do Red Hat Enterprise Linux a partir de uma imagem de PAYG do Azure Marketplace e utilizar RHUI alojado no Azure, vá para o [do Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Para saber mais sobre as imagens de Red Hat no Azure, vá para o [página de documentação](./rhel-images.md).
* Podem encontrar informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL no [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.
