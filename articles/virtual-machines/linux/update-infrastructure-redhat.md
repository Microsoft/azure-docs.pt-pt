---
title: Infraestrutura de atualização do Red Hat | Documentos da Microsoft
description: Saiba mais sobre a infraestrutura de atualização do Red Hat para instâncias de Red Hat Enterprise Linux a pedido no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 6b332af53f421230b3fb5401e525bd77c5e87ed9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081392"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização do Red Hat a pedido Red Hat Enterprise para VMs do Linux no Azure
 [Infraestrutura de atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite que os fornecedores de cloud, como o Azure, para espelhar o conteúdo do repositório alojado no Red Hat, criar repositórios personalizados com específica do Azure, conteúdo e disponibilizá-lo para VMs do utilizador final.

Imagens de pay as you go (PAYG) da Red Hat Enterprise Linux (RHEL) vêm pré-configurados para aceder a RHUI do Azure. Não é necessária nenhuma configuração adicional. Para obter as atualizações mais recentes, execute `sudo yum update` depois da sua instância do RHEL está pronta. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

Informações adicionais sobre as imagens RHEL no Azure, incluindo as políticas de publicação e retenção, estão disponíveis [aqui](./rhel-images.md).

Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o Azure RHUI

* O Azure RHUI é a infraestrutura de atualização que dá suporte a todas as VMs PAYG do RHEL criadas no Azure. Isso não impede que você registre suas VMs PAYG RHEL com o Gerenciador de assinaturas ou satélite ou outra fonte de atualizações, mas fazer isso com uma VM PAYG resultará em uma cobrança dupla indireta. Consulte o seguinte ponto para obter detalhes.
* Acesso para o RHUI alojado no Azure está incluído no preço de imagem RHEL PAYG. Se cancelar o registro de uma VM do RHEL PAYG a partir do RHUI alojado no Azure que não converter a máquina virtual num tipo bring-your-own-license (BYOL) da VM. Se se registra a mesma VM com outra origem de atualizações, pode incorrer _indireta_ duas vezes encargos. Será cobrado pela primeira vez para a taxa de software RHEL do Azure. Será cobrado na segunda vez para subscrições do Red Hat que foram adquiridas anteriormente. Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado pelo Azure, considere o registro para usar as [imagens do RHEL BYOS](https://aka.ms/rhel-byos).

* As imagens do RHEL SAP PAYG no Azure (RHEL for SAP, RHEL for SAP HANA e RHEL for SAP Business Applications) estão conectadas a canais de RHUI dedicados que permanecem na versão secundária do RHEL específica, conforme necessário para a certificação SAP.

* O acesso a RHUI alojado no Azure é limitado para as VMs dentro de [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se estiver a utilização do proxy todo o tráfego de VM por meio de uma infraestrutura de rede no local, poderá ter de configurar rotas definidas pelo utilizador para as VMs de PAYG RHEL acessar o RHUI do Azure. Se esse for o caso, as rotas definidas pelo usuário precisarão ser adicionadas para _todos os_ endereços IP RHUI.

## <a name="image-update-behavior"></a>Comportamento de atualização de imagem

A partir de abril de 2019, o Azure oferece imagens RHEL que estão conectadas a repositórios EUS (suporte de atualização estendida) por padrão e imagens RHEL que são conectadas aos repositórios regulares (não EUS) por padrão. Mais detalhes sobre o RHEL EUS estão disponíveis na documentação do [ciclo de vida da versão](https://access.redhat.com/support/policy/updates/errata) do Red Hat e na [documentação do eus](https://access.redhat.com/articles/rhel-eus). O comportamento padrão de `sudo yum update` variará dependendo de qual imagem RHEL provisionada, como imagens diferentes estão conectadas a repositórios diferentes.

Para obter uma lista de imagens completa `az vm image list --publisher redhat --all` , execute usando o CLI do Azure.

### <a name="images-connected-to-non-eus-repositories"></a>Imagens conectadas a repositórios não EUS

Se você provisionar uma VM de uma imagem RHEL conectada a repositórios não EUS, você será atualizado para a versão secundária mais recente do RHEL quando executar `sudo yum update`. Por exemplo, se você provisionar uma VM de uma imagem RHEL 7,4 PAYG `sudo yum update`e executar, você acabará com uma VM RHEL 7,7 (a versão secundária mais recente na família RHEL7).

As imagens que estão conectadas a repositórios não EUS não conterá um número de versão secundário na SKU. O SKU é o terceiro elemento no URN (nome completo da imagem). Por exemplo, todas as imagens a seguir são anexadas a repositórios não EUS:

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

Observe que os SKUs são 7-LVM ou 7-RAW. A versão secundária é indicada na versão (quarto elemento no URN) dessas imagens.

### <a name="images-connected-to-eus-repositories"></a>Imagens conectadas a repositórios do EUS

Se você provisionar uma VM de uma imagem RHEL conectada a repositórios EUS, você não será atualizado para a versão secundária mais recente do RHEL quando executar `sudo yum update`. Isso ocorre porque as imagens conectadas a repositórios EUS também são bloqueadas por versão para sua versão secundária específica.

As imagens conectadas a repositórios EUS conterá um número de versão secundário na SKU. Por exemplo, todas as imagens a seguir são anexadas a repositórios EUS:

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS e VMs RHEL de bloqueio de versão

Alguns clientes podem querer bloquear suas VMs RHEL para uma determinada versão secundária do RHEL depois de provisionar a VM. Você pode bloquear a versão da sua VM RHEL para uma versão secundária específica atualizando os repositórios para apontar para os repositórios de suporte de atualização estendida. Você também pode desfazer a operação de bloqueio de versão do EUS.

>[!NOTE]
> Não há suporte para EUS em extras de RHEL. Isso significa que, se você estiver instalando um pacote que geralmente está disponível no canal de extras do RHEL, não poderá fazer isso enquanto estiver em EUS. O ciclo de vida do produto Red Hat extras é detalhado [aqui](https://access.redhat.com/support/policy/updates/extras/).

No momento da redação deste artigo, o suporte do EUS foi encerrado para o RHEL < = 7,3. Consulte a seção "Red Hat Enterprise Linux mais suporte a Complementos" na documentação do [Red Hat](https://access.redhat.com/support/policy/updates/errata/) para obter mais detalhes.
* O suporte a RHEL 7,4 EUS termina em 31 de agosto de 2019
* O suporte a RHEL 7,5 EUS termina em 30 de abril de 2020
* O suporte a RHEL 7,6 EUS termina em 31 de outubro de 2020
* O suporte a RHEL 7,7 EUS termina em 30 de agosto de 2021

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>Alternar uma VM RHEL para EUS (bloqueio de versão para uma versão secundária específica)
Use as instruções a seguir para bloquear uma VM RHEL para uma versão secundária específica (executar como raiz):

>[!NOTE]
> Isso se aplica somente a versões RHEL para as quais o EUS está disponível. No momento da redação deste artigo, isso inclui o RHEL 7.2-7.7. Mais detalhes estão disponíveis na página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .

1. Desabilitar repositórios não EUS:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adicionar EUS repositórios:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloquear a variável releasever (executar como raiz):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueará a liberação secundária do RHEL para a versão secundária atual. Insira uma versão secundária específica se você pretende atualizar e bloquear para uma versão secundária posterior que não seja a mais recente. Por exemplo, `echo 7.5 > /etc/yum/vars/releasever` bloqueará sua versão do RHEL para RHEL 7,5

1. Atualizar sua VM RHEL
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>Mudar uma VM RHEL de volta para não EUS (remover um bloqueio de versão)
Execute o seguinte como raiz:
1. Remova o arquivo releasever:
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. Desabilitar EUS repositórios:
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. Configurar a VM RHEL
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```
    
1. Atualizar sua VM RHEL
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

## <a name="azure-rhui-infrastructure"></a>Infraestrutura RHUI do Azure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualizar o certificado de cliente do RHUI expirado numa VM

Se você estiver usando uma imagem de VM RHEL mais antiga, por exemplo, RHEL 7,4 (Urn `RedHat:RHEL:7.4:7.4.2018010506`de imagem:), você terá problemas de conectividade para RHUI devido a um certificado de cliente SSL agora expirado. O erro que você vê pode ser semelhante a _"o par SSL rejeitou seu certificado como expirado"_ ou _"erro: Não é possível recuperar os metadados do repositório (repomd. xml) para o repositório:... Verifique seu caminho e tente novamente "_ . Para resolver esse problema, atualize o pacote do cliente RHUI na VM usando o seguinte comando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Como alternativa, a `sudo yum update` execução também pode atualizar o pacote de certificado do cliente (dependendo da versão do RHEL), apesar dos erros de "certificado SSL expirado" que você verá para outros repositórios. Se essa atualização for bem-sucedida, a conectividade normal com outros repositórios do RHUI deverá ser restaurada, portanto, você poderá executar `sudo yum update` com êxito.

Se você encontrar um erro 404 ao executar um `yum update`, tente o seguinte para atualizar o cache yum:
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

### <a name="infrastructure-update"></a>Atualização de infraestrutura

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

## <a name="next-steps"></a>Passos Seguintes
* Para criar uma VM do Red Hat Enterprise Linux a partir de uma imagem de PAYG do Azure Marketplace e utilizar RHUI alojado no Azure, vá para o [do Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Para saber mais sobre as imagens do Red Hat no Azure, vá para a [página de documentação](./rhel-images.md).
* Informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL podem ser encontradas na página [ciclo de vida Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
