---
title: Resolução de problemas usando o comando dsregcmd - Diretório Ativo Azure
description: Utilização da saída de dsregcmd para entender o estado dos dispositivos em Azure AD
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
ms.openlocfilehash: 676a1dd2435d17db2151bdf21f1989e7f182701b
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136488"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Dispositivos de resolução de problemas utilizando o comando dsregcmd

O utilitário dsregcmd/status deve ser executado como uma conta de utilizador de domínio.

## <a name="device-state"></a>Estado do dispositivo

Esta secção lista os parâmetros de adesão do dispositivo. A tabela abaixo enumera os critérios para que o dispositivo esteja em vários estados de adesão.

| AzureAdJoined | EnterpriseJoined | Domínio Unido | Estado do dispositivo |
| ---   | ---   | ---   | ---   |
| SIM, SIM. | NO | NO | Azure AD Juntou-se |
| NO | NO | SIM, SIM. | Domínio Unido |
| SIM, SIM. | NO | SIM, SIM. | Anúncio Híbrido Juntou-se |
| NO | SIM, SIM. | SIM, SIM. | No local DRS Juntou-se |

> [!NOTE]
> O estado de Adesão ao Local de Trabalho (Azure AD registado) é apresentado na secção "Estado do Utilizador"

- **AzureAdJoined:** - set to "YES" se o dispositivo estiver unido ao Azure AD. "NÃO" de outra forma.
- **EnterpriseJoined:** - set to "YES" if the device is Joined to a on-premises DRS. Um dispositivo não pode ser EnterpriseJoined e AzureAdJoined.
- **DomainJoined:** - set to "YES" se o dispositivo estiver unido a um domínio (AD).
- **Nome do domínio:** - Definir o nome do domínio se o dispositivo estiver unido a um domínio.

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

Apresentado apenas quando o dispositivo é Azure AD aderiu ou ad AD híbrido Azure juntou-se (não azure AD registrado). Esta secção lista o dispositivo identificando detalhes armazenados na nuvem.

- **DispositivoId:** - Identificação única do dispositivo no inquilino da AD Azure
- **Impressão digital:** - Impressão digital do certificado do dispositivo 
- **Certificado de validade:** - Validade do certificado do dispositivo
- **KeyContainerId:** - Recipiente Id da chave privada do dispositivo associada ao certificado do dispositivo
- **KeyProvider:** - KeyProvider (Hardware/Software) utilizado para armazenar a chave privada do dispositivo.
- **TpmProtected:** - "SIM" se a chave privada do dispositivo for armazenada num TPM de hardware.

### <a name="sample-device-details-output"></a>Amostra de detalhes do dispositivo saída

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

Apresentado apenas quando o dispositivo é Azure AD aderiu ou ad AD híbrido Azure juntou-se (não azure AD registrado). Esta secção lista os detalhes comuns do inquilino quando um dispositivo é unido à Azure AD.

> [!NOTE]
> Se os URLs de MDM nesta secção estiverem vazios, indica que o MDM não foi configurado ou o utilizador atual não está no âmbito da inscrição do MDM. Verifique as definições de Mobilidade em Azure AD para rever a configuração do MDM.

> [!NOTE]
> Mesmo que veja sutiãs de MDM isto não significa que o dispositivo seja gerido por um MDM. A informação é apresentada se o inquilino tiver configuração de MDM para inscrição automática, mesmo que o dispositivo em si não seja gerido. 

### <a name="sample-tenant-details-output"></a>Amostra de dados do inquilino saída

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

Esta secção enumera o estado de vários atributos para o utilizador atualmente registado no dispositivo.

> [!NOTE]
> O comando deve ser executado num contexto de utilizador para recuperar o estado válido.

- **NgcSet:** - Definir para "SIM" se for definida uma tecla Windows Hello para a corrente registada no utilizador.
- **NgcKeyId:** - ID da tecla Windows Hello se uma estiver definida para a corrente registada no utilizador.
- **CanReset:** - Denota se a tecla Windows Hello pode ser redefinida pelo utilizador. 
- **Valores possíveis:** - DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive, ou Unknown if error. 
- **WorkplaceJoined:** - set to "YES" if Azure AD registered accounts have been added to the device in the current NTUSER context.
- **WamDefaultSet:** - Definir para "YES" se for criada uma WebAccount padrão WAM para o utilizador registado. Este campo pode apresentar um erro se o dsreg/estado for executado em contexto de administração. 
- **WamDefaultAuthority:** - set para "organizações" para Azure AD.
- **WamDefaultId:** - Sempre "https://login.microsoft.com" para Azure AD.
- **WamDefaultGUID:** - O guia do fornecedor WAM (conta Azure AD/Microsoft) para o WebAccount padrão da WAM. 

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

Esta secção pode ser ignorada para dispositivos registados em Azure AD.

> [!NOTE]
> O comando deve ser executado num contexto de utilizador para obter o estatuto válido para esse utilizador.

- **AzureAdPrt:** - Definir para "SIM" se um PRT estiver presente no dispositivo para o utilizador ligado.
- **AzureAdPrtUpdateTime:** - Definido até ao momento em UTC quando o PRT foi atualizado pela última vez.
- **AzureAdPrtExpiryTime:** - Definido até ao momento em UTC quando o PrT expirar se não for renovado.
- **AzureAdPrtAuthority:** - Azure AD authority URL
- **EnterprisePrt:** - Definir para "SIM" se o dispositivo tiver PRT a partir do local ADFS. Para dispositivos híbridos Azure AD, o dispositivo pode ter PRT tanto a partir de AD Azure como de anúncios no local simultaneamente. No local, os dispositivos aderidos terão apenas um PRT da Enterprise.
- **EnterprisePrtUpdateTime:** - Definido até ao momento em UTC quando o ENTERPRISE PRT foi atualizado pela última vez.
- **EnterprisePrtExpiryTime:** - Definir o tempo na UTC quando o PRT vai expirar se não for renovado.
- **EnterprisePrtAuthority:** - URL de autoridade da ADFS

### <a name="sample-sso-state-output"></a>Amostra De saída do estado SSO

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

### <a name="pre-join-diagnostics"></a>Pré-aderir diagnósticos

Esta secção só é apresentada se o dispositivo for unido ao domínio e não conseguir aderir a Azure AD híbrido.

Esta secção realiza vários testes para ajudar a diagnosticar falhas de adesão. Esta secção também inclui os detalhes do anterior (?). Esta informação inclui a fase de erro, o código de erro, o ID do pedido do servidor, o estado do servidor http, a mensagem de erro de resposta do servidor.

- Contexto do **utilizador:** - O contexto em que os diagnósticos são executados. Valores possíveis: SISTEMA, Utilizador NÃO-ELEVADO, Utilizador ELEVADO. 

   > [!NOTE]
   > Uma vez que a adesão real é realizada em contexto SYSTEM, executar os diagnósticos em contexto SYSTEM é mais próximo do cenário de adesão real. Para executar diagnósticos no contexto DOSISTEMA, o comando dsregcmd/status deve ser executado a partir de um pedido de comando elevado.

- **Hora do Cliente:** - O tempo do sistema na UTC.
- **Teste de Conectividade AD:** - Teste realiza um teste de conectividade ao controlador de domínio. Erro neste teste provavelmente resultará em Erros de Juntar na fase de pré-verificação.
- **Teste de Configuração AD:** - O teste lê e verifica se o objeto SCP está configurado corretamente na floresta ad- local. Erros neste teste provavelmente resultariam em Erros de Juntar na fase de descoberta com o código de erro 0x801c001d.
- **DRS Discovery Test:** - O teste obtém os pontos finais do DRS a partir do ponto final dos metadados de descoberta e executa um pedido de reino do utilizador. Erros neste teste provavelmente resultariam em Erros de Juntar na fase de descoberta.
- Teste de **conectividade DRS:** - Teste realiza teste básico de conectividade ao ponto final do DRS.
- Teste de **aquisição token:** - Teste tenta obter um símbolo de autenticação Azure AD se o inquilino utilizador for federado. Erros neste teste provavelmente resultariam em erros de auth Na fase auth. Se a auth falhar a sincronização será tentada como recuo, a menos que o recuo seja explicitamente desativado com as definições da chave de registo abaixo.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Fallback to Sync-Join:** - set to "Enabled" se a chave de registo acima, para evitar que o recuo para sincronizar se junte a falhas auth, NÃO está presente. Esta opção está disponível a partir do Windows 10 1803 e posteriormente.
- **Registo prévio:** - Tempo da tentativa de Adsão anterior ocorreu. Só as tentativas falhadas de join são registadas.
- **Fase de Erro:** - A fase da adesão em que foi abortada. Os valores possíveis são pré-verificar, descobrir, auth, juntar-se.
- **Código de erro do cliente:** - Código de erro do cliente devolvido (HRESULT).
- **Código de erro** do servidor: - Código de erro do servidor se um pedido foi enviado para o servidor e o servidor respondeu com um código de erro. 
- **Mensagem do servidor:** - Mensagem do servidor devolvida juntamente com o código de erro.
- **Estado https:** - Estado de http devolvido pelo servidor.
- **ID pedido:** - O pedido do clienteId enviado para o servidor. Útil para correlacionar com os registos do lado do servidor.

### <a name="sample-pre-join-diagnostics-output"></a>Amostra de pré-aderir à saída de diagnósticos

O exemplo que se segue mostra o teste de diagnóstico falhando com um erro de descoberta.

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

O exemplo que se segue mostra que os testes de diagnóstico estão a passar, mas a tentativa de registo falhou com um erro de diretório, que se espera que se junte a sincronização. Assim que o trabalho de sincronização azure AD Connect terminar, o dispositivo poderá aderir.

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

### <a name="post-join-diagnostics"></a>Diagnósticos pós-adesão

Esta secção exibe a saída de verificações de sanidade realizadas num dispositivo ligado à nuvem.

- **AadRecoveryEnabled:** - Se "SIM", as chaves armazenadas no dispositivo não são utilizáveis e o dispositivo está marcado para recuperação. O próximo sinal irá desencadear o fluxo de recuperação e reregistar o dispositivo.
- **TecladoSignTest:** - Se "PASS" as teclas do dispositivo estiverem de boa saúde. Se o KeySignTest falhar, o dispositivo será normalmente marcado para a recuperação. O próximo sinal irá desencadear o fluxo de recuperação e reregistar o dispositivo. Para dispositivos híbridos Azure AD a recuperação é silenciosa. Enquanto a Azure AD se juntou ou a Azure AD foi registada, os dispositivos solicitarão a autenticação do utilizador para recuperar e reregistar o dispositivo, se necessário. **O KeySignTest requer privilégios elevados.**

#### <a name="sample-post-join-diagnostics-output"></a>Amostra de pós-aderir de diagnósticos

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Verificação pré-requisito ngc

Esta secção realiza as verificações perquisite para o fornecimento do Windows Hello for Business (WHFB). 

> [!NOTE]
> Pode não ver os dados de verificação pré-requisito da NGC em dsregcmd /status se o utilizador já configurado com sucesso WHFB.

- **IsDeviceJoined:** - set to "YES" se o dispositivo estiver unido ao Azure AD.
- **IsUserAzureAD:** - Definir para "SIM" se o utilizador registado estiver presente no Azure AD .
- **PolíticaActivada:** - Definir para "SIM" se a política WHFB estiver ativada no dispositivo.
- **PostLogonEnabled:** - set to "YES" se a inscrição whFB for desencadeada de forma nativa pela plataforma. Se for definido como "NO", indica que a inscrição do Windows Hello for Business é desencadeada por um mecanismo personalizado
- **DispositivoELegível:** - Definir para "SIM" se o dispositivo cumprir o requisito de hardware para se inscrever com WHFB.
- **SessionIsNotRemote:** - Desloque-se para "SIM" se o utilizador atual estiver ligado diretamente ao dispositivo e não remotamente.
- **CertInscriç:** - Específico para a implantação do WhFB Certificate Trust, indicando a autoridade de inscrição do certificado para whfb. Definido para "autoridade de inscrição" se fonte da política WHFB for Política de Grupo, "gestão de dispositivos móveis" se a fonte for MDM. "nenhum" de outra forma
- **AdfsRefreshToken:** - Específico para a implantação do WhFB Certificate Trust. Só presente se a CertEnrollment for "autoridade de inscrição". Indica se o dispositivo tem um PRT empresarial para o utilizador.
- **AdfsRaIsReady:** - Específico para a implementação do WhFB Certificate Trust.  Só presente se a CertEnrollment for "autoridade de inscrição". Set para "YES" se a ADFS indicar nos metadados de descoberta que suporta WHFB *e* se o modelo de certificado de logon estiver disponível.
- **LogonCertTemplateReady:** - Específico para a implementação do WhFB Certificate Trust. Só presente se a CertEnrollment for "autoridade de inscrição". Definir para "SIM" se o modelo de certificado de logon for válido e ajudar a resolver problemas ADFS RA.
- **PreReqResult:** - Fornece resultado de toda a avaliação pré-requisito whFB. Definido para "Will Provision" se a inscrição WHFB for lançada como uma tarefa pós-início quando o utilizador assinar na próxima vez.

### <a name="sample-ngc-prerequisite-check-output"></a>Amostra NGC pré-requisito saída de verificação

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

Para perguntas, consulte as FAQ de [gestão de dispositivos](faq.md)
