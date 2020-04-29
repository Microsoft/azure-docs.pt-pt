---
title: Configurar configurações de proxy do serviço de mobilidade para a recuperação de desastres de Azure para Azure [ Microsoft Docs
description: Fornece detalhes sobre como configurar o serviço de mobilidade quando os clientes usam um proxy no seu ambiente de origem.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 3d33b5a89a718a41e5c547551f6e7eb4f7033a63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503130"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Configurar configurações de proxy do serviço de mobilidade para a recuperação de desastres de Azure para Azure

Este artigo fornece orientações sobre a personalização das configurações de networking na máquina virtual Azure (VM) alvo quando estiver a replicar e recuperar VMs Azure de uma região para outra, utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)

O objetivo deste documento é fornecer passos para configurar configurações de proxy para o Serviço de Mobilidade de Recuperação de Sítios Azure no cenário de Recuperação de Desastres Azure-Azure. 

Os proxies são gateways de rede que permitem/não permitir ligações de rede a pontos finais. Tipicamente, um proxy é uma máquina fora da máquina cliente que tenta aceder aos pontos finais da rede. Uma lista de bypass permite ao cliente fazer ligações diretamente aos pontos finais sem passar pelo proxy. Um nome de utilizador e uma palavra-passe podem ser opcionalmente definidos para um proxy por administradores de rede para que apenas os clientes autenticados possam usar procuração. 

## <a name="before-you-start"></a>Antes de começar

Saiba como a Recuperação do Site proporciona recuperação de desastres para [este cenário.](azure-to-azure-architecture.md)
Compreenda a [orientação](azure-to-azure-about-networking.md) de networking quando estiver a replicar e recuperar VMs Azure de uma região para outra, utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)
Certifique-se de que o seu representante está configurado adequadamente com base nas necessidades da sua organização.

## <a name="configure-the-mobility-service"></a>Configure o Serviço de Mobilidade

O Serviço de Mobilidade suporta apenas proxies não autenticados. Fornece duas formas de introduzir detalhes de procuração para comunicação com pontos finais de Recuperação do Site. 

### <a name="method-1-auto-detection"></a>Método 1: Deteção automática

O Serviço de Mobilidade deteta automaticamente as definições de procuração a partir de configurações de ambiente ou definições de IE (Apenas windows) durante a replicação ativa. 

- Windows OS: Durante a replicação ativa, o Serviço de Mobilidade deteta as definições de procuração configuradas no Internet Explorer para o utilizador do Sistema Local. Para configurar a conta proxy para o Sistema Local, um administrador pode usar o psexec para lançar um pedido de comando e, em seguida, o Internet Explorer. 
- Windows OS: As definições de procuração são configuradas à medida que as variáveis ambientais http_proxy e no_proxy. 
- Linux OS: As definições de procuração são configuradas em /etc/perfil ou /etc/ambiente como variáveis ambientais http_proxy, no_proxy. 
- As definições de proxy detetadas automaticamente são guardadas para o proxy config file ProxyInfo.conf do Serviço de Mobilidade 
- Localização padrão de ProxyInfo.conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Método 2: Fornecer definições de proxy de aplicação personalizadas

Neste caso, o cliente fornece configurações personalizadas de proxy de aplicação no ficheiro config ProxyInfo.conf do Serviço de Mobilidade. Este método permite que os clientes forneçam procuração apenas para o Serviço de Mobilidade ou um representante diferente para o Serviço de Mobilidade de Recuperação do Sítio Azure do que um proxy (ou nenhum representante) para o resto das aplicações na máquina.

## <a name="proxy-template"></a>Modelo de procuração
ProxyInfo.conf contém o seguinte modelohttp://1.2.3.4 [proxy] Address= Porta=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. O BypassList não suporta wildcards como '*.windows.net' mas dar windows.net é bom o suficiente para contornar. 

## <a name="next-steps"></a>Passos seguintes:
- Leia [orientação de networking](site-recovery-azure-to-azure-networking-guidance.md) para replicar VMs Azure.
- Implementar a recuperação de desastres [replicando VMs Azure](site-recovery-azure-to-azure.md).