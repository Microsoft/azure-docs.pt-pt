---
title: Pré-requisitos para o fornecimento de nuvem azure AD Connect em Azure AD
description: Este artigo descreve os pré-requisitos e requisitos de hardware que necessita para o fornecimento de nuvens.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55f2167552e21973d304f98693be022683fdf661
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870939"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Pré-requisitos para o fornecimento de nuvem azure AD Connect
Este artigo fornece orientações sobre como escolher e utilizar o Azure Ative Directory (Azure AD) Connect cloud provisioning como a sua solução de identidade.



## <a name="cloud-provisioning-agent-requirements"></a>Requisitos do agente de fornecimento de nuvem
Você precisa do seguinte para usar o fornecimento de nuvem Azure AD Connect:
    
- Uma conta de administrador global para o seu inquilino Azure AD que não é um utilizador convidado.
- Um servidor no local para o agente de provisionamento com o Windows 2012 R2 ou posterior.
- Configurações de firewall no local.

>[!NOTE]
>Atualmente, o agente de provisionamento só pode ser instalado em servidores de língua inglesa. A instalação de um pacote de língua inglesa num servidor não inglês não é uma suver válida e resultará na não instalação do agente. 

O resto do documento fornece instruções passo a passo para estes pré-requisitos.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração do Diretório Ativo Azure

1. Crie uma conta de administrador global apenas em nuvem no seu inquilino Azure AD. Desta forma, pode gerir a configuração do seu inquilino se os seus serviços no local falharem ou ficarem indisponíveis. Saiba como adicionar uma conta de [administrador global só para a nuvem.](../active-directory-users-create-azure-portal.md) Terminar este passo é fundamental para garantir que não seja trancado fora do seu inquilino.
1. Adicione um ou mais nomes de [domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu inquilino Azure AD. Os seus utilizadores podem iniciar sessão com um destes nomes de domínio.

### <a name="in-your-directory-in-active-directory"></a>No seu diretório em Diretório Ativo

Executar a [ferramenta IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) para preparar os atributos de diretório para sincronização.

### <a name="in-your-on-premises-environment"></a>No seu ambiente no local

1. Identifique um servidor de anfitriões associado ao domínio que execute o Windows Server 2012 R2 ou superior com um mínimo de 4-GB de RAM e .NET 4.7.1+ tempo de execução.

1. A política de execução powerShell no servidor local deve ser definida como Indefinida ou RemotaMente.

1. Se houver uma firewall entre os seus servidores e a AD Azure, configure os seguintes itens:
   - Certifique-se de que os agentes podem fazer pedidos de *saída* à Azure AD sobre as seguintes portas:

        | Número da porta | Como é usado |
        | --- | --- |
        | **80** | Descarrega as listas de revogação do certificado (CRLs) enquanto valida o certificado TLS/SSL.  |
        | **443** | Lida com toda a comunicação de saída com o serviço. |
        | **8080** (opcional) | Os agentes reportam o seu estado a cada 10 minutos sobre o porto 8080, se a porta 443 não estiver disponível. Este estado é apresentado no portal Azure AD. |
     
   - Se a sua firewall aplicar regras de acordo com os utilizadores originários, abra estas portas para tráfego a partir de serviços Windows que funcionam como um serviço de rede.
   - Se a sua firewall ou proxy permitir especificar sufixos seguros, adicione ligações a \* .msappproxy.net e \* .servicebus.windows.net. Caso contrário, permita o acesso às gamas IP do Centro de [Dados Azure,](https://www.microsoft.com/download/details.aspx?id=41653)que são atualizadas semanalmente.
   - Os seus agentes precisam de acesso a login.windows.net e login.microsoftonline.com para registo inicial. Abra a sua firewall para os URLs também.
   - Para validação de certificados, desbloqueie os seguintes URLs: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 e www \. microsoft.com:80. Estes URLs são usados para validação de certificados com outros produtos da Microsoft, pelo que pode já ter estes URLs desbloqueados.

>[!NOTE]
> A instalação do agente de fornecimento de nuvem no Windows Server Core não é suportada.


### <a name="additional-requirements"></a>Requisitos adicionais
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisitos tLS

>[!NOTE]
>Transporte Layer Security (TLS) é um protocolo que prevê comunicações seguras. A alteração das definições de TLS afeta toda a floresta. Para mais informações, consulte [o Update para ativar tLS 1.1 e TLS 1.2 como protocolos de segurança predefinidos no WinHTTP no Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

O servidor Windows que acolhe o agente de fornecimento de nuvem Azure AD Connect deve ter TLS 1.2 ativado antes de o instalar.

Para ativar o TLS 1.2, siga estes passos.

1. Detete as seguintes teclas de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor.


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)

