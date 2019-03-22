---
title: Infraestrutura de atualização do Red Hat | Documentos da Microsoft
description: Saiba mais sobre a infraestrutura de atualização do Red Hat para instâncias de Red Hat Enterprise Linux a pedido no Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 1/7/2019
ms.author: borisb
ms.openlocfilehash: 2fc881aac096ccbafa351fcac2d726cc51d8f178
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286895"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização do Red Hat a pedido Red Hat Enterprise para VMs do Linux no Azure
 [Infraestrutura de atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite que os fornecedores de cloud, como o Azure, para espelhar o conteúdo do repositório alojado no Red Hat, criar repositórios personalizados com específica do Azure, conteúdo e disponibilizá-lo para VMs do utilizador final.

Imagens de pay as you go (PAYG) da Red Hat Enterprise Linux (RHEL) vêm pré-configurados para aceder a RHUI do Azure. Não é necessária nenhuma configuração adicional. Para obter as atualizações mais recentes, execute `sudo yum update` depois da sua instância do RHEL está pronta. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

Estão disponíveis informações adicionais sobre imagens RHEL no Azure, incluindo a publicação e políticas de retenção [aqui](./rhel-images.md).

Podem encontrar informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL no [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o Azure RHUI
* Azure RHUI atualmente suporta apenas a versão mais recente de pequenas em cada família RHEL (RHEL6 ou RHEL7). Para atualizar uma instância de RHEL VM ligada ao RHUI para a versão secundária mais recente, executar `sudo yum update`.

    Por exemplo, se aprovisionar uma VM a partir de uma imagem RHEL 7.4 PAYG e execute `sudo yum update`, acaba tendo uma VM do RHEL 7,6 (a versão mais recente secundária da família de RHEL7).

    Para evitar este comportamento, o que necessita para criar sua própria imagem, conforme descrito no [criar e carregar uma máquina de virtual baseada em Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo. Em seguida, precisa para ligá-lo para uma infraestrutura de atualização diferentes ([diretamente ao Red Hat conteúdo servidores de entrega](https://access.redhat.com/solutions/253273) ou uma [server da satélite do Red Hat](https://access.redhat.com/products/red-hat-satellite)).

* Acesso para o RHUI alojado no Azure está incluído no preço de imagem RHEL PAYG. Se cancelar o registro de uma VM do RHEL PAYG a partir do RHUI alojado no Azure que não converter a máquina virtual num tipo bring-your-own-license (BYOL) da VM. Se se registra a mesma VM com outra origem de atualizações, pode incorrer _indireta_ duas vezes encargos. Será cobrado pela primeira vez para a taxa de software RHEL do Azure. Será cobrado na segunda vez para subscrições do Red Hat que foram adquiridas anteriormente. Se tiver consistentemente de utilizar uma infraestrutura de atualização que não seja RHUI alojado no Azure, considere criar e implementar as suas próprias imagens (tipo de BYOL). Este processo é descrito em [criar e carregar uma máquina de virtual baseada em Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Imagens RHEL PAYG de SAP no Azure (de RHEL for SAP, de RHEL for SAP HANA e de RHEL for SAP Business Applications) estão ligadas a canais RHUI dedicados que permanecem na versão secundária de RHEL específica conforme necessário para a certificação da SAP.

* O acesso a RHUI alojado no Azure é limitado para as VMs dentro de [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se estiver a utilização do proxy todo o tráfego de VM por meio de uma infraestrutura de rede no local, poderá ter de configurar rotas definidas pelo utilizador para as VMs de PAYG RHEL acessar o RHUI do Azure.

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS e VMs do RHEL de bloqueio de versão
Alguns clientes talvez queira bloquear suas VMs do RHEL para uma versão de determinados RHEL pequenas. Pode o bloqueio de versão a VM RHEL para uma versão específica de pequenas, atualizando os repositórios para que apontem para os repositórios de suporte estendido de atualização. Utilize as seguintes instruções para bloquear uma VM RHEL para a versão secundária específica:

>[!NOTE]
> Isto aplica-se apenas para versões do RHEL para o qual EUS está disponível. No momento da redação deste artigo, isto inclui o RHEL 7.2 7,6. Estão disponíveis em mais detalhes a [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.

1. Desative repositórios não EUS:
    ```bash
    sudo yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. Adicione repositórios EUS:
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. Bloquear a variável releasever:
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > A instrução acima bloqueará a versão secundária do RHEL para a versão atual de pequenas. Introduza uma versão secundária específica, se estiver à procura a atualização e de bloqueio para a posterior pequenas versão que não é a versão mais recente. Por exemplo, `echo 7.5 > /etc/yum/vars/releasever` bloqueará a sua versão RHEL para RHEL 7.5

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

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Infraestrutura do RHUI do Azure


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>Atualizar o certificado de cliente do RHUI expirado numa VM

Se estiver a utilizar uma imagem de RHEL VM mais antiga, por exemplo, RHEL 7.4 (imagem URN: `RedHat:RHEL:7.4:7.4.2018010506`), terá problemas de conectividade para RHUI devido a um certificado de cliente SSL expirado por agora. O erro que vê poderá ter o aspeto _"ponto a ponto do SSL rejeitou o certificado como expirada"_ ou _"erro: Não é possível obter metadados de repositório (repomd.xml) para o repositório:... Verifique o caminho e tente novamente"_. Para superar esse problema, atualize o pacote de cliente do RHUI na VM com o seguinte comando:

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

Em alternativa, em execução `sudo yum update` também pode atualizar o pacote de certificado de cliente (dependendo da versão RHEL), apesar de erros de "certificado SSL expirado" verá para outros repositórios. Se esta atualização for bem-sucedida, conectividade normal para outros repositórios do RHUI deve ser restaurada, por isso, será capaz de executar `sudo yum update` com êxito.


### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Resolver problemas de ligação para o Azure RHUI
Se tiver problemas a ligar ao Azure RHUI da sua VM do Azure RHEL PAYG, siga estes passos:

1. Verifique a configuração de VM para o ponto de final do RHUI do Azure:

    a. Verifique se o `/etc/yum.repos.d/rh-cloud.repo` arquivo contém uma referência ao `rhui-[1-3].microsoft.com` no `baseurl` da `[rhui-microsoft-azure-rhel*]` secção do ficheiro. Se assim for, está usando o novo RHUI do Azure.

    b. Se ele aponta para uma localização com o seguinte padrão, `mirrorlist.*cds[1-4].cloudapp.net`, é necessária uma atualização de configuração. Estiver a utilizar o antigo instantâneo VM e terá de atualizá-lo para apontar para o novo RHUI do Azure.

1. Acesso a RHUI alojado no Azure está limitado a VMs dentro de [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

1. Se estiver a utilizar a nova configuração, ter verificado que a VM liga-se de que o intervalo de IP do Azure e ainda não é possível ligar ao Azure RHUI, o ficheiro de um incidente de suporte com a Microsoft ou do Red Hat.

### <a name="infrastructure-update"></a>Atualização de infra-estrutura

Em Setembro de 2016, Implementámos uma RHUI atualizados do Azure. Em Abril de 2017, vamos encerrar o antigo RHUI do Azure. Se usa as imagens RHEL PAYG (ou respetivos instantâneos) de Setembro de 2016 ou posterior, está a ligar automaticamente para o novo RHUI do Azure. Se, no entanto, tiver instantâneos mais antigos nas suas VMs, terá de atualizar manualmente a configuração para aceder a RHUI de Azure conforme descrito na seção a seguir.

Os novos servidores do RHUI do Azure são implementados com [Gestor de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). No Gestor de tráfego, um único ponto final (rhui 1.microsoft.com) pode ser utilizado por qualquer VM, independentemente da região.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para utilizar os servidores do RHUI do Azure
Este procedimento é fornecido apenas para referência. Imagens RHEL PAYG já tem a configuração correta para se ligar ao Azure RHUI. Para atualizar manualmente a configuração para utilizar os servidores do RHUI do Azure, conclua os seguintes passos:

1. Transferir a assinatura de chave pública através de curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc
   ```

1. Verificar a validade da chave transferida.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

1. Verificar a saída e, em seguida, verifique se o `keyid` e o `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

1. Instale a chave pública.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

1. Transferir, certifique-se e instalar um cliente do Gestor de pacote de RPM (RPM).

    >[!NOTE]
    >Alterar a versões de pacotes. Se se conectar manualmente à RHUI do Azure, pode encontrar a versão mais recente do pacote de cliente para cada família RHEL ao aprovisionar a imagem mais recente a partir da galeria.

   a. Transferir.

    - Para RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.2-74.noarch.rpm
        ```

    - Para RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.2-74.noarch.rpm
        ```

   b. Certifique-se.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Verificar a saída para se certificar de que a assinatura do pacote está OK.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Instale o RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

1. Depois de concluir, certifique-se de que podem aceder ao Azure RHUI da VM.

## <a name="next-steps"></a>Passos Seguintes
* Para criar uma VM do Red Hat Enterprise Linux a partir de uma imagem de PAYG do Azure Marketplace e utilizar RHUI alojado no Azure, vá para o [do Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/).
* Para saber mais sobre as imagens de Red Hat no Azure, vá para o [página de documentação](./rhel-images.md).
* Podem encontrar informações sobre as políticas de suporte do Red Hat para todas as versões do RHEL no [ciclo de vida do Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) página.
