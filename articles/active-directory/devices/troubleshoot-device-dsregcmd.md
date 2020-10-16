---
title: Resolução de problemas utilizando o comando dsregcmd - Azure Ative Directory
description: Usando a saída de dsregcmd para entender o estado dos dispositivos em Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cd782cdab625934fe60617142e5ac0baf756398
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80128768"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Dispositivos de resolução de problemas utilizando o comando dsregcmd

O utilitário dsregcmd /status deve ser executado como uma conta de utilizador de domínio.

## <a name="device-state"></a>Estado do dispositivo

Esta secção lista os parâmetros de junção do estado. A tabela abaixo enumera os critérios para que o dispositivo se desmente em vários estados de adesão.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Estado do dispositivo |
| ---   | ---   | ---   | ---   |
| SIM | NO | NO | Azure AD juntou-se |
| NO | NO | SIM | Domínio Unidos |
| SIM | NO | SIM | Anúncio híbrido juntou-se |
| NO | SIM | SIM | No local DRS Unidos |

> [!NOTE]
> O estado de Azure AD (registrado Azure AD) é apresentado na secção "Estado utilizador"

- **AzureAdJoined:** - set to "YES" if the device is Joined to Azure AD. "NÃO" de outra forma.
- **EnterpriseJoined:** - Definir para "SIM" se o dispositivo estiver ligado a um DRS no local. Um dispositivo não pode ser tanto o EnterpriseJoined como o AzureAdJoined.
- **DomainJoined:** - Definir para "SIM" se o dispositivo estiver associado a um domínio (AD).
- **Nome de domínio:** - Definir o nome do domínio se o dispositivo estiver unido a um domínio.

### <a name="sample-device-state-output"></a>Saída do estado do dispositivo de amostra

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Detalhes do dispositivo

Apresentado apenas quando o dispositivo é Azure AD aderido ou híbrido Azure AD (não Azure AD registrado). Esta secção lista os detalhes de identificação do dispositivo armazenados na nuvem.

- **DeviceId:** - ID exclusivo do dispositivo no inquilino AZure AD
- **Impressão digital:** - Impressão digital do certificado do dispositivo 
- **DeviceCertificateValidity:** - Validade do certificado do dispositivo
- **KeyContainerId:** - ContentorId da chave privada do dispositivo associada ao certificado do dispositivo
- **KeyProvider:** - KeyProvider (Hardware/Software) usado para armazenar a chave privada do dispositivo.
- **TpmProtegido:** - "SIM" se a chave privada do dispositivo estiver armazenada num TPM de hardware.

### <a name="sample-device-details-output"></a>Saída de detalhes do dispositivo de amostra

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Detalhes do inquilino

Apresentado apenas quando o dispositivo é Azure AD aderido ou híbrido Azure AD (não Azure AD registrado). Esta secção lista os detalhes comuns do inquilino quando um dispositivo é associado ao Azure AD.

> [!NOTE]
> Se os URLs MDM desta secção estiverem vazios, indica que o MDM não foi configurado ou o utilizador atual não está no âmbito da inscrição do MDM. Consulte as definições de Mobilidade em Azure AD para rever a sua configuração de MDM.

> [!NOTE]
> Mesmo que veja URLs MDM isto não significa que o dispositivo seja gerido por um MDM. A informação é apresentada se o arrendatário tiver configuração MDM para inscrição automática, mesmo que o próprio dispositivo não seja gerido. 

### <a name="sample-tenant-details-output"></a>Amostra de produção de detalhes do inquilino

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Estado do utilizador

Esta secção lista o estado de vários atributos para o utilizador atualmente iniciado no dispositivo.

> [!NOTE]
> O comando deve ser executado num contexto de utilizador para recuperar o estado válido.

- **NgcSet:** - Definir para "SIM" se uma tecla Windows Hello estiver definida para a corrente iniciada no utilizador.
- **NgcKeyId:** - ID da tecla Windows Hello se for definido para o corrente registado no utilizador.
- **CanReset:** - Denota se a tecla Windows Hello pode ser reiniciada pelo utilizador. 
- **Valores possíveis:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive, ou Unknown if error. 
- **WorkplaceJoined:** - Definir para "SIM" se as contas registadas da AZure AD tiverem sido adicionadas ao dispositivo no contexto atual do NTUSER.
- **WamDefaultSet:** - Definir para "YES" se for criada uma Contagem Web padrão da WAM para o utilizador registado. Este campo pode apresentar um erro se o dsreg /status for executado a partir de uma solicitação de comando elevada. 
- **WamDefaultAuthority:** - set to "organizations" for Azure AD.
- **WamDefaultId:** - Always https://login.microsoft.com " para Azure AD.
- **WamDefaultGUID:** - O fornecedor WAM (conta Azure AD/Microsoft) GUID para a contagem web padrão WAM. 

### <a name="sample-user-state-output"></a>Saída do estado do utilizador da amostra

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Estado SSO

Esta secção pode ser ignorada para dispositivos registados Azure AD.

> [!NOTE]
> O comando deve ser executado num contexto de utilizador para obter o estado válido para esse utilizador.

- **AzureAdPrt:** - Definir para "SIM" se um PRT estiver presente no dispositivo para o utilizador iniciado.
- **AzureAdPrtUpdateTime:** - Defina a hora na UTC quando o PRT foi atualizado pela última vez.
- **AzureAdPrtExpiryTime:** - Desajei a hora na UTC quando o PRT vai expirar se não for renovado.
- **AzureAdPrtAuthority:** - Azure AD authority URL
- **EnterprisePrt:** - Definir para "SIM" se o dispositivo tiver PRT a partir de ADFS no local. Para dispositivos híbridos Azure AD ligados, o dispositivo poderia ter PRT tanto da AD AZure como da AD no local simultaneamente. Os dispositivos de ação no local só terão um PRT da Empresa.
- **EnterprisePrtUpdateTime:** - Definir para a hora na UTC quando o PRT da empresa foi atualizado pela última vez.
- **EnterprisePrtExpiryTime:** - Desajei a hora na UTC quando o PRT vai expirar se não for renovado.
- **EnterprisePrtAuthority:** - ADFS Authority URL

### <a name="sample-sso-state-output"></a>Amostra de saída do estado SSO

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Dados de diagnóstico

### <a name="pre-join-diagnostics"></a>Diagnósticos pré-aderir

Esta secção só é apresentada se o dispositivo estiver unido ao domínio e não for capaz de juntar a AD híbrida.

Esta secção realiza vários testes para ajudar a diagnosticar falhas de junção. Esta secção também inclui os detalhes do anterior (?). Esta informação inclui a fase de erro, o código de erro, o ID do pedido do servidor, o estado http da resposta do servidor, a mensagem de erro de resposta do servidor.

- **Contexto do utilizador:** - O contexto em que os diagnósticos são executados. Valores possíveis: Sistema, Utilizador não elevado, Utilizador elevado. 

   > [!NOTE]
   > Uma vez que a união real é realizada em contexto SYSTEM, executar os diagnósticos em contexto SYSTEM é o mais próximo do cenário de união real. Para executar diagnósticos em contexto SYSTEM, o comando dsregcmd /status deve ser executado a partir de uma solicitação de comando elevada.

- **Tempo do Cliente:** - O tempo do sistema na UTC.
- **Teste de Conectividade AD:** - O teste realiza um teste de conectividade ao controlador de domínio. Erro neste teste provavelmente resultará em erros de junção na fase de pré-verificação.
- Teste de **configuração de AD:** - O teste lê e verifica se o objeto SCP está configurado corretamente na floresta AD no local. Erros neste teste provavelmente resultariam em erros de Junção na fase de descoberta com o código de erro 0x801c001d.
- **DRS Discovery Test:** - O teste obtém os pontos finais de DRS do ponto final de discovery metadados e executa um pedido de reino do utilizador. Erros neste teste provavelmente resultariam em erros de Junção na fase de descoberta.
- **Teste de conectividade DRS:** - O teste realiza um teste básico de conectividade ao ponto final do DRS.
- **Teste de aquisição de token:** - O teste tenta obter um sinal de autenticação AD AZure se o inquilino do utilizador for federado. Erros neste teste provavelmente resultariam em erros de Junção na fase auth. Se a auth falhar a sincronização, a junção será tentada como recuo, a menos que o recuo seja explicitamente desativado com as definições de chave de registo abaixo.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Recuo para Sync-Join:** - Definir para "Ativado" se a tecla de registo acima, para evitar que o recuo se junte a falhas de auth, NÃO esteja presente. Esta opção encontra-se disponível a partir do Windows 10 1803 e posteriormente.
- **Registo anterior:** - Tempo ocorreu a tentativa anterior de Join. Apenas as tentativas falhadas de Junção são registadas.
- **Fase de Erro:** - Fase da junção em que foi abortado. Os valores possíveis são pré-verificação, descobrir, auth, juntar-se.
- **Código de erro do cliente:** - Código de erro do cliente devolvido (HRESULT).
- **Código de erro do servidor:** - Código de erro do servidor se um pedido foi enviado para o servidor e o servidor respondeu com um código de erro. 
- **Mensagem do servidor:** - Mensagem do servidor devolvida juntamente com o código de erro.
- **Estado de https:** - Estado de http devolvido pelo servidor.
- **ID do pedido:** - O pedido de clienteId enviado para o servidor. Útil para correlacionar com registos do lado do servidor.

### <a name="sample-pre-join-diagnostics-output"></a>Saída de diagnóstico pré-adere à amostra

O exemplo a seguir mostra que o teste de diagnóstico falha com um erro de descoberta.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

O exemplo a seguir mostra que os testes de diagnóstico estão a passar, mas a tentativa de registo falhou com um erro de diretório, que se espera para a sincronização. Uma vez concluído o trabalho de sincronização Azure AD Connect, o dispositivo poderá aderir.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnósticos pós-aderem

Esta secção apresenta a saída das verificações de sanidade realizadas num dispositivo ligado à nuvem.

- **AadRecoveryEnabled:** - Se "SIM", as teclas armazenadas no dispositivo não são utilizáveis e o dispositivo está marcado para recuperação. O próximo sinal irá desencadear o fluxo de recuperação e voltar a registar o dispositivo.
- **KeySignTest:** - Se "PASSED" as teclas do dispositivo estiverem de boa saúde. Se o KeySignTest falhar, o dispositivo será normalmente marcado para recuperação. O próximo sinal irá desencadear o fluxo de recuperação e voltar a registar o dispositivo. Para os dispositivos híbridos Azure AD a unição a recuperação é silenciosa. Enquanto a Azure AD se juntou ou a Azure AD registou, os dispositivos solicitarão que a autenticação do utilizador recupere e volte a registar o dispositivo, se necessário. **O KeySignTest requer privilégios elevados.**

#### <a name="sample-post-join-diagnostics-output"></a>Saída de diagnóstico pós-aderi de amostra

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Verificação pré-requisito da NGC

Esta secção realiza os controlos perquisitos para o provisionamento do Windows Hello for Business (WHFB). 

> [!NOTE]
> Pode não ver os detalhes de verificação pré-requisitos da NGC em dsregcmd/status se o utilizador já configurar com sucesso o WHFB.

- **IsDeviceJoined:** - set to "YES" if the device is join to Azure AD.
- **IsUserAzureAD:** - Definir para "SIM" se o utilizador registado estiver presente no Azure AD .
- **PolicyEnabled:** - Definir para "SIM" se a política WHFB estiver ativada no dispositivo.
- **PostLogonEnabled:** - Definir para "SIM" se a inscrição whfb for desencadeada de forma nativa pela plataforma. Se estiver definido para "NO", indica que a inscrição no Windows Hello for Business é desencadeada por um mecanismo personalizado
- **DeviceEligible:** - Definir para "SIM" se o dispositivo cumprir o requisito de hardware para a inscrição no WHFB.
- **SessionIsNotRemote:** - Definir para "SIM" se o utilizador atual tiver iniciado sessão diretamente no dispositivo e não remotamente.
- **CertEnrollment:** - Envio específico do WHFB Certificate Trust, indicando a autoridade de inscrição de certificados para o WHFB. Definida como "autoridade de inscrição" se a fonte da política WHFB for a Política de Grupo, "gestão de dispositivos móveis" se a fonte for MDM. "nenhum" de outra forma
- **AdfsRefreshToken:** - Implantação específica do WHFB Certificate Trust. Só estão presentes se o CertEnrollment for "autoridade de inscrição". Indica se o dispositivo tem um PRT da empresa para o utilizador.
- **AdfsRaIsReady:** - Implantação específica do WHFB Certificate Trust.  Só estão presentes se o CertEnrollment for "autoridade de inscrição". Definido como "SIM" se a ADFS indicar na descoberta metadados que suporta o WHFB *e* se o modelo de certificado de logon estiver disponível.
- **LogonCertTemplateReady:** - Implantação específica do WHFB Certificate Trust. Só estão presentes se o CertEnrollment for "autoridade de inscrição". Definido para "SIM" se o estado do modelo de certificado de logon for válido e ajudar a resolver problemas ADFS RA.
- **Pré-ReqResult:** - Fornece o resultado de toda a avaliação pré-requisito do WHFB. Definido como "Will Provision" se a inscrição do WHFB for lançada como uma tarefa pós-início de sé quando o utilizador assinar na próxima vez.

### <a name="sample-ngc-prerequisite-check-output"></a>Amostra NGC pré-requisito de saída de verificação

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Passos seguintes

Para dúvidas, consulte a [faq de gestão](faq.md) de dispositivos
