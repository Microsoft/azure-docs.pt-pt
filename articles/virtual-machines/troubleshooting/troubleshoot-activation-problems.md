---
title: Solucionar problemas de ativação de máquina virtual do Windows no Azure | Microsoft Docs
description: Fornece as etapas de solução de problemas para corrigir problemas de ativação de máquina virtual do Windows no Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: a1c2049d7355ab946dbf426ec71f7f6178b8f153
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819098"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Solucionar problemas de ativação de máquina virtual do Windows do Azure

Se você tiver problemas ao ativar a VM (máquina virtual) do Windows do Azure que é criada a partir de uma imagem personalizada, poderá usar as informações fornecidas neste documento para solucionar o problema. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Noções básicas sobre pontos de extremidade do KMS do Azure para a ativação do Windows de máquinas virtuais do Azure

O Azure usa pontos de extremidade diferentes para a ativação do KMS (serviços de gerenciamento de chaves), dependendo da região da nuvem onde reside a VM. Ao usar este guia de solução de problemas, use o ponto de extremidade KMS apropriado que se aplica à sua região.

* Regiões de nuvem pública do Azure: kms.core.windows.net:1688
* Regiões da nuvem nacional do Azure China 21Vianet: kms.core.chinacloudapi.cn:1688
* Regiões de nuvem nacional do Azure Alemanha: kms.core.cloudapi.de:1688
* Regiões de nuvem nacional US Gov do Azure: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Sintoma

Ao tentar ativar uma VM do Windows Azure, você receberá uma mensagem de erro semelhante à seguinte amostra:

**Erro: 0xC004F074 o serviço de software relatou que o computador não pôde ser ativado. Nenhum ManagementService de chave (KMS) pôde ser contatado. Consulte o log de eventos do aplicativo para obter informações adicionais.**

## <a name="cause"></a>Causa

Em geral, os problemas de ativação de VM do Azure ocorrem se a VM do Windows não estiver configurada usando a chave de instalação do cliente KMS apropriada ou se a VM do Windows tiver um problema de conectividade com o serviço KMS do Azure (kms.core.windows.net, porta 1688). 

## <a name="solution"></a>Solução

>[!NOTE]
>Se você estiver usando uma VPN site a site e um túnel forçado, consulte [usar rotas personalizadas do Azure para habilitar a ativação do KMS com túnel forçado](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Se você estiver usando o ExpressRoute e tiver uma rota padrão publicada, consulte [a VM do Azure pode falhar ao ser ativada por meio do ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Etapa 1 configurar a chave de instalação do cliente KMS apropriada

Para a VM que é criada com base em uma imagem personalizada, você deve configurar a chave de instalação do cliente KMS apropriada para a VM.

1. Execute **slmgr. vbs/dlv** em um prompt de comandos com privilégios elevados. Verifique o valor da descrição na saída e determine se ele foi criado na mídia de licença de varejo (canal de varejo) ou volume (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Se **slmgr.vbs /dlv** mostrar o canal RETAIL, execute os comandos seguintes para definir a [chave de configuração do cliente KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) relativa à versão do Windows Server que está a ser utilizada e force a repetição da ativação: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Por exemplo, para o Windows Server 2016 datacenter, você executaria o seguinte comando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Etapa 2 verificar a conectividade entre a VM e o serviço KMS do Azure

1. Baixe e extraia a ferramenta [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) para uma pasta local na VM que não é ativada. 

2. Vá para iniciar, pesquise no Windows PowerShell, clique com o botão direito do mouse em Windows PowerShell e selecione executar como administrador.

3. Confirme que a VM está configurada para utilizar o servidor correto do KMS do Azure. Para fazer isso, execute o seguinte comando:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    O comando deve retornar: nome do computador do serviço de gerenciamento de chaves definido como kms.core.windows.net:1688 com êxito.

4. Verifique usando Psping que você tem conectividade com o servidor KMS. Mude para a pasta para a qual extraiu a transferência de Pstools.zip e execute o seguinte:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   Na segunda linha da saída, verifique se você vê: sent = 4, Received = 4, Lost = 0 (0% de perda).

   Se a perda for maior que 0 (zero), a VM não terá conectividade com o servidor KMS. Nessa situação, se a VM estiver em uma rede virtual e tiver um servidor DNS personalizado especificado, você deverá verificar se o servidor DNS é capaz de resolver kms.core.windows.net. Ou então, altere o servidor DNS para um que resolva kms.core.windows.net.

   Observe que, se você remover todos os servidores DNS de uma rede virtual, as VMs usarão o serviço DNS interno do Azure. Esse serviço pode resolver kms.core.windows.net.
  
    Além disso, verifique se o tráfego de rede de saída para o ponto de extremidade KMS com a porta 1688 não está bloqueado pelo firewall na VM.

5. Depois de verificar a conectividade bem-sucedida com o kms.core.windows.net, execute o seguinte comando no prompt do Windows PowerShell com privilégios elevados. Este comando repete a ativação várias vezes.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Uma ativação com êxito devolve informações semelhantes ao seguinte:
    
    **Ativando o Windows (R), ServerDatacenter Edition (12345678-1234-1234-1234-12345678)...  Produto ativado com êxito.**

## <a name="faq"></a>FAQ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Criei o Windows Server 2016 do Azure Marketplace. Preciso configurar a chave KMS para ativar o Windows Server 2016? 

 
Não. A imagem no Azure Marketplace tem a chave de instalação do cliente KMS apropriada já configurada. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>A ativação do Windows funciona da mesma maneira, independentemente de a VM estar usando o benefício de uso híbrido do Azure (HUB) ou não? 

 
Sim. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>O que acontece se o período de ativação do Windows expirar? 

 
Quando o período de carência tiver expirado e o Windows ainda não estiver ativado, o Windows Server 2008 R2 e versões posteriores do Windows mostrarão notificações adicionais sobre a ativação. O papel de parede da área de trabalho permanece preto e Windows Update instalará somente atualizações críticas e de segurança, mas não as atualizações opcionais. Consulte a seção notificações na parte inferior da página [condições de licenciamento](https://technet.microsoft.com/library/ff793403.aspx) .   

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.
