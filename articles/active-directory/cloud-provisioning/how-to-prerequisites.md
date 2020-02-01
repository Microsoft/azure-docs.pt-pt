---
title: Pré-requisitos para o provisionamento Azure AD Connect Cloud no Azure AD
description: Este artigo descreve os pré-requisitos e os requisitos de hardware necessários para o provisionamento na nuvem.
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
ms.openlocfilehash: 51e5c58d29f01cadcc3ea2e8ec48ae67e58c4180
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909045"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Pré-requisitos para o provisionamento do Azure AD Connect Cloud
Este artigo fornece orientação sobre como escolher e usar o provisionamento de nuvem do Azure Active Directory (Azure AD) Connect como sua solução de identidade.



## <a name="cloud-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento de nuvem
Você precisa do seguinte para usar Azure AD Connect provisionamento de nuvem:
    
- Uma conta de administrador global para seu locatário do Azure AD.
- Um servidor local para o agente de provisionamento com o Windows 2012 R2 ou posterior.
- Configurações de firewall local.

>[!NOTE]
>Atualmente, o agente de provisionamento só pode ser instalado em servidores de língua inglesa. A instalação de um pacote de língua inglesa num servidor não inglês não é uma suver válida e resultará na não instalação do agente. 

O restante do documento fornece instruções passo a passo para esses pré-requisitos.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração Azure Active Directory

1. Crie uma conta de administrador global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário se os serviços locais falharem ou ficarem indisponíveis. Saiba mais sobre como [Adicionar uma conta de administrador global somente em nuvem](../active-directory-users-create-azure-portal.md). A conclusão desta etapa é essencial para garantir que você não fique bloqueado do seu locatário.
1. Adicione um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor de host ingressado no domínio que executa o Windows Server 2012 R2 ou superior com um mínimo de 4 GB de RAM e do tempo de execução do .NET 4.7.1 +.

1. Se houver um firewall entre seus servidores e o Azure AD, configure os seguintes itens:
   - Verifique se os agentes podem fazer solicitações de *saída* para o Azure ad nas seguintes portas:

        | Número da porta | Como é usado |
        | --- | --- |
        | **80** | Baixa as listas de certificados revogados (CRLs) ao validar o certificado SSL.  |
        | **443** | Manipula toda a comunicação de saída com o serviço. |
        | **8080** (opcional) | Os agentes relatarão seu status a cada 10 minutos pela porta 8080, se a porta 443 não estiver disponível. Esse status é exibido no portal do AD do Azure. |
     
   - Se o firewall impõe regras de acordo com os usuários de origem, abra essas portas para o tráfego de serviços do Windows que são executados como um serviço de rede.
   - Se o firewall ou o proxy permitir que você especifique os sufixos seguros, adicione conexões a \*. msappproxy.net e \*. servicebus.windows.net. Caso contrário, permita o acesso aos [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente.
   - Seus agentes precisam de acesso a login.windows.net e login.microsoftonline.com para o registro inicial. Abra seu firewall para essas URLs também.
   - Para a validação de certificado, desbloqueie as seguintes URLs: mscrl.microsoft.com:80, crl.microsoft.com:80, ocsp.msocsp.com:80 e www\.microsoft.com:80. Essas URLs são usadas para validação de certificado com outros produtos da Microsoft, portanto, talvez você já tenha essas URLs desbloqueadas.

### <a name="verify-the-port"></a>Verificar a porta
Para verificar se o Azure está escutando na porta 443 e se o agente pode se comunicar com ele, use a seguinte URL:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Esse teste verifica se os agentes podem se comunicar com o Azure pela porta 443. Abra um navegador e vá para a URL anterior do servidor em que o agente está instalado.

![Verificação de acessibilidade de porta](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Requisitos adicionais
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisitos de TLS

>[!NOTE]
>O TLS é um protocolo que fornece comunicações seguras. A alteração das configurações de TLS afeta toda a floresta. Para obter mais informações, consulte [atualizar para habilitar o tls 1,1 e o tls 1,2 como protocolos de segurança padrão no WinHTTP no Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

O Windows Server que hospeda o agente de provisionamento de nuvem Azure AD Connect deve ter o TLS 1,2 habilitado antes de você instalá-lo.

Para habilitar o TLS 1,2, siga estas etapas.

1. Defina as seguintes chaves de registo:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reiniciar o servidor.


## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)

