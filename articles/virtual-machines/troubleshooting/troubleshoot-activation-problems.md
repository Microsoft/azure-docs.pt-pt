---
title: Resolução de problemas Problemas de ativação de máquina virtual do Windows em Azure Microsoft Docs
description: Fornece os passos de resolução de problemas para corrigir problemas de ativação de máquinas virtuais do Windows no Azure
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
ms.openlocfilehash: 987d4c7188c2bdc2ba6264805e33b79e7d2851d6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966292"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Troubleshoot Azure Windows virtual machine activation problems (Resolver problemas de ativação de máquinas virtuais do Windows no Azure)

Se tiver problemas ao ativar a máquina virtual (VM) do Azure Windows que é criada a partir de uma imagem personalizada, pode utilizar as informações fornecidas neste documento para resolver problemas. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Compreender os pontos finais do Azure KMS para ativação de produtos Windows das Máquinas Virtuais do Azure

O Azure utiliza diferentes pontos finais para a ativação kms (Key Management Services) dependendo da região de nuvem onde reside o VM. Ao utilizar este guia de resolução de problemas, utilize o ponto final KMS apropriado que se aplica à sua região.

* Regiões de nuvens públicas azure: kms.core.windows.net:1688
* Azure China 21Vianet regiões de nuvem nacional: kms.core.chinacloudapi.cn:1688
* Regiões de nuvens nacionais da Alemanha: kms.core.cloudapi.de:1688
* Regiões de nuvens nacionais do Azure US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Sintoma

Quando tenta ativar um VM do Azure Windows, recebe uma mensagem de erro que se assemelha à seguinte amostra:

**Erro: 0xC004F074 O Software LicensingService informou que o computador não pôde ser ativado. Nenhum Key ManagementService (KMS) poderia ser contactado. Consulte o Registo de Eventos de Aplicação para obter informações adicionais.**

## <a name="cause"></a>Causa

De um modo geral, os problemas de ativação de VMs do Azure ocorrerão se a VM do Windows não for configurada com a chave de configuração de cliente KMS adequada ou se a VM do Windows tiver um problema de conectividade ao serviço KMS do Azure (kms.core.windows.net, porta 1688). 

## <a name="solution"></a>Solução

>[!NOTE]
>Se estiver a utilizar uma VPN local e a fazer túneis forçados, consulte [as rotas personalizadas use Azure para permitir a ativação kms com túneis forçados](../../vpn-gateway/vpn-gateway-about-forced-tunneling.md). 
>
>Se estiver a utilizar o ExpressRoute e tiver uma rota padrão publicada, veja [se posso bloquear a conectividade da Internet com redes virtuais ligadas aos circuitos ExpressRoute?](../../expressroute/expressroute-faqs.md)

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Passo 1 Configurar a chave de configuração adequada do cliente KMS

Para o VM que é criado a partir de uma imagem personalizada, você deve configurar a chave de configuração do cliente KMS apropriado para o VM.

1. Executar **slmgr.vbs /dlv** num pedido de comando elevado. Verifique o valor descrição na saída e, em seguida, determine se foi criado a partir de meios de licença de retalho (canal de retalho) ou volume (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Se **slmgr.vbs /dlv** mostrar o canal RETAIL, execute os comandos seguintes para definir a [chave de configuração do cliente KMS](/windows-server/get-started/kmsclientkeys) relativa à versão do Windows Server que está a ser utilizada e force a repetição da ativação: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Por exemplo, para o Centro de Dados do Windows Server 2016, executaria o seguinte comando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Passo 2 Verifique a conectividade entre o serviço VM e Azure KMS

1. Faça o download e extrafim a ferramenta [PSping](/sysinternals/downloads/psping) para uma pasta local no VM que não é ativada. 

2. Ir para Iniciar, pesquisar no Windows PowerShell, clique com o botão direito Windows PowerShell e, em seguida, selecione Executar como administrador.

3. Confirme que a VM está configurada para utilizar o servidor correto do KMS do Azure. Para tal, execute o seguinte comando:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    O comando deve regressar: O nome da máquina do Serviço de Gestão de Chaves definido para kms.core.windows.net:1688 com sucesso.

4. Utilize PSping para confirmar que tem conectividade ao servidor do KMS. Mude para a pasta para a qual extraiu a transferência de Pstools.zip e execute o seguinte:
  
    ```
    .\psping.exe kms.core.windows.net:1688
    ```
   Na segunda e última linha da saída, certifique-se de que vê: Sent = 4, Recebido = 4, Perdido = 0 (perda de 0%).

   Se Lost for superior a 0 (zero), o VM não tem conectividade com o servidor KMS. Nesta situação, se o VM estiver numa rede virtual e tiver um servidor DNS personalizado especificado, deve certificar-se de que o servidor DNS é capaz de resolver kms.core.windows.net. Ou, mude o servidor DNS para um que resolva kms.core.windows.net.

   Note que se remover todos os servidores DNS de uma rede virtual, os VMs usam o serviço DNS interno da Azure. Este serviço pode resolver kms.core.windows.net.
  
    Certifique-se também de que o tráfego da rede de saída para o ponto final KMS com a porta 1688 não está bloqueado pela firewall no VM.

5. Verifique através [do Network Watcher Next Hop](../../network-watcher/network-watcher-next-hop-overview.md) que o próximo tipo de lúpulo do VM em questão para o destino IP 23.102.135.246 (para kms.core.windows.net) ou o IP do ponto final kms apropriado que se aplica à sua região é a **Internet.**  Se o resultado for VirtualAppliance ou VirtualNetworkGateway, é provável que exista uma rota padrão.  Contacte o administrador da rede e trabalhe com eles para determinar o curso correto de ação.  Esta pode ser uma [rota personalizada](./custom-routes-enable-kms-activation.md) se essa solução for consistente com as políticas da sua organização.

6. Depois de verificar a conectividade a kms.core.windows.net, execute o comando seguinte nessa linha de comandos elevada do Windows PowerShell. Este comando repete a ativação várias vezes.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Uma ativação com êxito devolve informações semelhantes ao seguinte:
    
    **Ativação do Windows(R), edição ServerDatacenter (12345678-1234-1234-1234-12345678) ...  Produto ativado com sucesso.**

## <a name="faq"></a>FAQ 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>Criei o Windows Server 2016 a partir do Azure Marketplace. Preciso de configurar a chave KMS para ativar o Windows Server 2016? 

 
Não. A imagem no Azure Marketplace tem a chave de configuração do cliente KMS já configurada. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>A ativação do Windows funciona da mesma forma, independentemente de o VM estar a utilizar ou não o Benefício de Utilização Híbrida Azure (HUB) ou não? 

 
Yes. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>O que acontece se o período de ativação do Windows expirar? 

 
Quando o período de carência tiver expirado e o Windows ainda não estiver ativado, o Windows Server 2008 R2 e as versões posteriores do Windows mostrarão notificações adicionais sobre a ativação. O papel de parede do ambiente de trabalho permanece preto e o Windows Update instalará apenas atualizações de segurança e críticas, mas não atualizações opcionais. Consulte a secção de Notificações na parte inferior da página [Condições de Licenciamento.](/previous-versions/tn-archive/ff793403(v=technet.10))   

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contacte o suporte.

Se ainda precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver o seu problema rapidamente.