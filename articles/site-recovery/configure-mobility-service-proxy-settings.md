---
title: Configurar configurações de procuração do Serviço de Mobilidade para Azure to Azure Disaster Recovery / Microsoft Docs
description: Fornece detalhes sobre como configurar o serviço de mobilidade quando os clientes usam um proxy no seu ambiente de origem.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86133224"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Configurar configurações de procuração do serviço de mobilidade para a recuperação de desastres de Azure

Este artigo fornece orientações sobre a personalização de configurações de rede na máquina virtual Azure (VM) alvo quando está a replicar e a recuperar VMs Azure de uma região para outra, utilizando a Recuperação do [Local Azure.](site-recovery-overview.md)

O objetivo deste documento é fornecer medidas para configurar definições de procuração para o Serviço de Mobilidade de Recuperação de Sítios Azure no cenário Azure to Azure Disaster Recovery. 

Os proxies são gateways de rede que permitem/não permitem ligações de rede aos pontos finais. Normalmente, um proxy é uma máquina fora da máquina do cliente que tenta aceder aos pontos finais da rede. Uma lista de bypass permite ao cliente fazer ligações diretamente com os pontos finais sem passar pelo representante. Um nome de utilizador e palavra-passe podem ser configurados opcionalmente para um proxy por administradores de rede para que apenas clientes autenticados possam usar proxy. 

## <a name="before-you-start"></a>Antes de começar

Saiba como a Recuperação do Site proporciona recuperação de desastres para [este cenário.](azure-to-azure-architecture.md)
Compreenda a [orientação de networking](azure-to-azure-about-networking.md) quando estiver a replicar e a recuperar VMs Azure de uma região para outra, utilizando [a recuperação do local de Azure](site-recovery-overview.md).
Certifique-se de que o seu representante está configurado adequadamente com base nas necessidades da sua organização.

## <a name="configure-the-mobility-service"></a>Configure o Serviço de Mobilidade

O Serviço de Mobilidade suporta apenas proxies não autenticados. Fornece duas formas de introduzir detalhes de procuração para comunicação com os pontos finais de recuperação do local. 

### <a name="method-1-auto-detection"></a>Método 1: Deteção automática

O Serviço de Mobilidade automática deteta as definições de procuração a partir de configurações de ambiente ou definições de IE (Apenas Windows) durante a replicação. 

- Windows OS: Durante a ativação da replicação, o Serviço de Mobilidade deteta as definições de procuração configuradas no Internet Explorer para o utilizador do Sistema Local. Para configurar o proxy para a conta do Sistema Local, um administrador pode usar o psexec para lançar uma solicitação de comando e, em seguida, o Internet Explorer. 
- Windows OS: As definições de procuração são configuradas à medida que as variáveis ambientais http_proxy e no_proxy. 
- Linux OS: As definições de procuração são configuradas em /etc/perfil ou /etc/ambiente como variáveis ambientais http_proxy, no_proxy. 
- As definições de procuração detetadas automaticamente são guardadas no Serviço de Mobilidade Proxy Proxy Config file ProxyInfo.conf 
- Localização padrão de ProxyInfo.conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux: /usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Método 2: Fornecer configurações personalizadas de procuração de aplicações

Neste caso, o cliente fornece configurações personalizadas de procuração de aplicações no Serviço de Mobilidade config ficheiro ProxyInfo.conf. Este método permite que os clientes forneçam procuração apenas para o Serviço de Mobilidade ou um proxy diferente para o Serviço de Mobilidade de Recuperação do Local Azure do que um proxy (ou nenhum representante) para o resto das aplicações na máquina.

## <a name="proxy-template"></a>Modelo de procuração
ProxyInfo.conf contém o seguinte modelo [proxy] Endereço= http://1.2.3.4 Port=5678 BypassList=hypervrecoverymanager.windowsazure.com,login.microsoftonline.com,blob.core.windows.net. O BypassList não suporta wildcards como '*.windows.net', mas dar windows.net é bom o suficiente para contornar. 

## <a name="next-steps"></a>Passos seguintes:
- Leia [orientação de rede](./azure-to-azure-about-networking.md)  para replicar VMs Azure.
- Implementar a recuperação de [desastres replicando VMs Azure](./azure-to-azure-quickstart.md).
