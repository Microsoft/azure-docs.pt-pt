---
title: Pré-requisitos para o provisionamento Azure AD Connect Cloud no Azure AD
description: Este tópico descreve os pré-requisitos e os requisitos de hardware do provisionamento de nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 155edf72a60e079a609853e953e3cf66024cc83c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794253"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Pré-requisitos para o provisionamento do Azure AD Connect Cloud
Este tópico fornece orientação sobre como escolher e usar o provisionamento de nuvem Azure AD Connect como sua solução de identidade.



## <a name="cloud-provisioning-agent-requirements"></a>Requisitos do agente de provisionamento de nuvem
Você precisará do seguinte para usar Azure AD Connect provisionamento de nuvem:
    
- uma conta de administrador global para seu locatário do Azure AD
- um servidor local para o agente de provisionamento com o Windows 2012 R2 ou posterior
- configurações de firewall local

O restante do documento fornecerá instruções passo a passo para esses pré-requisitos.

### <a name="in-the-azure-active-directory-admin-center"></a>No centro de administração Azure Active Directory

1. Crie uma conta de administrador global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre como [Adicionar uma conta de administrador global somente em nuvem](../active-directory-users-create-azure-portal.md). A conclusão desta etapa é essencial para garantir que você não fique bloqueado do seu locatário.
2. Adicione um ou mais [nomes de domínio personalizados](../active-directory-domains-add-azure-portal.md) ao seu locatário do Azure AD. Os usuários podem entrar com um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identidade de um servidor de host ingressado no domínio que executa o Windows Server 2012 R2 ou superior com o mínimo de 4 GB de RAM e do tempo de execução do .NET 4.7.1 + 

2. Se houver um firewall entre seus servidores e o Azure AD, configure os seguintes itens:
   - Verifique se os agentes podem fazer solicitações de *saída* para o Azure ad nas seguintes portas:

     | Número da porta | Como é usado |
     | --- | --- |
     | **80** | Baixa as listas de certificados revogados (CRLs) ao validar o certificado SSL |
     | **443** | Manipula toda a comunicação de saída com o serviço |
     | **8080** (opcional) | Os agentes relatarão seu status a cada dez minutos pela porta 8080, se a porta 443 não estiver disponível. Esse status é exibido no portal do AD do Azure. A porta 8080 _não_ é usada para entradas de usuário. |
     
     Se o firewall impõe regras de acordo com os usuários de origem, abra essas portas para o tráfego de serviços do Windows que são executados como um serviço de rede.
   - Se o seu firewall ou proxy permitir que você especifique os sufixos seguros, adicione conexões a **\*. msappproxy.net** e **\*. ServiceBus.Windows.net**. Caso contrário, permita o acesso aos [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente.
   - Seus agentes precisam de acesso a **login.Windows.net** e **login.microsoftonline.com** para o registro inicial. Abra seu firewall para essas URLs também.
   - Para a validação de certificado, desbloqueie as seguintes URLs: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**e **www\.Microsoft.com:80**. Como essas URLs são usadas para validação de certificado com outros produtos da Microsoft, você pode já ter essas URLs desbloqueadas.

### <a name="verify-the-port"></a>Verificar a porta
Para verificar se o Azure está escutando na porta 443 e se o agente pode se comunicar com ele, você pode usar o seguinte:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Esse teste verificará se os agentes podem se comunicar com o Azure pela porta 443.  Abra um navegador e navegue até a URL acima do servidor em que o agente está instalado.
![Serviços](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>Requisitos adicionais
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>Requisitos de TLS

>[!NOTE]
>A TLS (segurança de camada de transporte) é um protocolo que fornece comunicações seguras.  A alteração das configurações de TLS afeta toda a floresta.  Para obter mais informações [, consulte atualizar para habilitar o tls 1,1 e o tls 1,2 como protocolos de segurança padrão no WinHTTP no Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)

O Windows Server que hospedará o Azure AD Connect agente de provisionamento de nuvem precisa ter o TLS 1,2 habilitado antes de você instalá-lo.

Para habilitar o TLS 1,2:

1. Defina as seguintes chaves do registro:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Reinicie o servidor.


## <a name="next-steps"></a>Passos seguintes 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é Azure AD Connect provisionamento de nuvem?](what-is-cloud-provisioning.md)

