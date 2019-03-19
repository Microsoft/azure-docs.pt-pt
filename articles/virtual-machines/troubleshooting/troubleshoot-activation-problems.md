---
title: Resolução de problemas de ativação de máquina virtual de Windows no Azure | Documentos da Microsoft
description: Fornece os passos de resolução de problemas para corrigir problemas de ativação de máquina virtual do Windows no Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: 0f700b9e24399768977a1fa221322fa4c1c6708d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095148"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Resolução de problemas de ativação de máquina virtual do Windows Azure

Se tiver problemas ao ativar a máquina de virtual de Windows Azure (VM) que é criada a partir de uma imagem personalizada, pode utilizar as informações fornecidas neste documento para resolver o problema. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Noções básicas sobre a pontos finais do Azure KMS para ativação do produto Windows das máquinas virtuais do Azure
O Azure utiliza diferentes pontos de extremidade para ativação do KMS consoante a região de cloud em que a VM reside. Ao utilizar este guia de resolução de problemas, utilize o ponto de final adequado do KMS aplica-se a sua região.

* Regiões de cloud pública do Azure: kms.core.windows.net:1688
* Azure China 21Vianet national cloud regions: kms.core.chinacloudapi.cn:1688
* Regiões de cloud nacional do Azure Alemanha: kms.core.cloudapi.de:1688
* Regiões de cloud nacional do Azure gov (US): kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Sintoma

Quando tenta ativar uma VM do Windows Azure, recebe um erro mensagem é semelhante ao exemplo seguinte:

**Erro: 0xC004F074 que o LicensingService de Software reportou que não foi possível ativar o computador. Foi possível contactar não ManagementService de chaves (KMS). Consulte o registo de eventos de aplicativo para obter informações adicionais.**

## <a name="cause"></a>Causa
Em geral, os problemas de ativação de VM do Azure ocorrerem se a VM do Windows não está configurada, utilizando a chave de configuração de cliente KMS adequada ou se a VM do Windows tem um problema de conectividade ao serviço Azure KMS (kms.core.windows.net, porta 1688). 

## <a name="solution"></a>Solução

>[!NOTE]
>Se estiver a utilizar uma VPN de site a site e imposição de túnel, veja [rotas personalizadas do Azure de utilização para ativar a ativação do KMS com túnel forçado](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx). 
>
>Se estiver a utilizar o ExpressRoute e tiver uma rota padrão publicadas, consulte [VM do Azure pode falhar ao ativar através do ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key-for-windows-server-2016-and-windows-server-2012-r2"></a>Passo 1 configurar a chave de configuração de cliente KMS apropriada (para Windows Server 2016 e Windows Server 2012 R2)

Para a VM criada a partir de uma imagem personalizada do Windows Server 2016 ou Windows Server 2012 R2, tem de configurar a chave de configuração de cliente KMS adequada para a VM.

Este passo não é aplicável ao Windows 2012 ou Windows 2008 R2. Ele usa a funcionalidade de ativação de Máquina Virtual de automatização (AVMA), que é suportada apenas pelo Windows Server 2016 e Windows Server 2012 R2.

1. Execute **Slmgr. vbs /dlv** numa linha de comandos elevada. Verifique o valor de descrição na saída e, em seguida, determinar se ela foi criada a partir de mídia de licença de volume (VOLUME_KMSCLIENT) ou de varejo (canal de varejo):
  
    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Se **slmgr.vbs /dlv** mostrar o canal RETAIL, execute os comandos seguintes para definir a [chave de configuração do cliente KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) relativa à versão do Windows Server que está a ser utilizada e force a repetição da ativação: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Por exemplo, para o Windows Server 2016 Datacenter, executaria o seguinte comando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Passo 2-verificar a conectividade entre o serviço VM e KMS do Azure

1. Transferir e extrair os [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) ferramenta para uma pasta local na VM que não ativa. 

2. Volte a iniciar, procure no Windows PowerShell, Windows PowerShell com o botão direito e, em seguida, selecionar executar como administrador.

3. Confirme que a VM está configurada para utilizar o servidor correto do KMS do Azure. Para tal, execute o seguinte comando:
  
    ```
    iex "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```
    O comando deve devolver: Nome da máquina do serviço de gestão de chaves definida para kms.core.windows.net:1688 com êxito.

4. Certifique-se ao utilizar o Psping que tem conetividade ao servidor do KMS. Mude para a pasta para a qual extraiu a transferência de Pstools.zip e execute o seguinte:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
  
   Na penúltima linha da saída, verifique se vê: Enviado = 4, recebidos = 4, perdida = 0 (perda de 0%).

   Se perdida for superior a 0 (zero), a VM tem conetividade ao servidor do KMS. Nesta situação, é capaz de resolver kms.core.windows.net se a VM estiver numa rede virtual e tiver um servidor DNS personalizado especificado, deve certificar-se de que o servidor DNS. Em alternativa, altere o servidor DNS de forma que resolver kms.core.windows.net.

   Observe que, se remover todos os servidores DNS de uma rede virtual, as VMs usam o serviço DNS interno do Azure. Este serviço pode resolver kms.core.windows.net.
  
Certifique-se também de que a firewall de convidado não foi configurada de forma que faria a bloquear tentativas de ativação.

1. Depois de verificar a conectividade com êxito kms.core.windows.net, execute o seguinte comando essa linha de comandos elevada do Windows PowerShell. Este comando repete a ativação várias vezes.

    ```
    1..12 | % { iex “$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato” ; start-sleep 5 }
    ```

Uma ativação com êxito devolve informações semelhantes ao seguinte:

**Ativar o Windows (r), edição de ServerDatacenter (12345678-1234-1234-1234-12345678)... Produto ativado com êxito.**

## <a name="faq"></a>FAQ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Criei o Windows Server 2016 no Azure Marketplace. É necessário configurar a chave KMS para ativação do Windows Server 2016? 
 
Não. A imagem no Azure Marketplace tem a chave de configuração do cliente KMS apropriada já configurada. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>Ativação do Windows funciona da mesma forma independentemente se a VM estiver a utilizar benefício de utilização de híbrida (HUB) do Azure ou não? 
 
Sim. 
 
### <a name="what-happens-if-windows-activation-period-expires"></a>O que acontece em caso de expiração do período de ativação do Windows? 
 
Quando o período de tolerância expirou e o Windows ainda não se encontra ativado, o Windows Server 2008 R2 e versões posteriores do Windows irão mostrar notificações adicionais como ativar o Azure. O papel de parede da área de trabalho fica preta e atualização do Windows instalará a segurança e apenas as atualizações críticas, mas as atualizações não opcionais. Consulte a secção de notificações na parte inferior a [condições de licenciamento](https://technet.microsoft.com/library/ff793403.aspx) página.   

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.
Se precisar de ajuda, ainda [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para a sua questão resolvidos rapidamente.
