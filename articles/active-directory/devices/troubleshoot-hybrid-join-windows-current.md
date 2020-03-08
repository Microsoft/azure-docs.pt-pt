---
title: Resolução de problemas híbrido Azure Ative Directory juntou-se a dispositivos
description: O Diretório Ativo Azure Ative de resolução de problemas juntou-se aos dispositivos Windows 10 e Windows Server 2016.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3ce27c59ead4e126cb143d1831ece0e93e119ef
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672249"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Resolução de problemas híbrido Azure Ative Directory juntou-se a dispositivos 

O conteúdo deste artigo é aplicável aos dispositivos que executam o Windows 10 ou o Windows Server 2016.

Para outros clientes do Windows, consulte o artigo [Troubleshooting hybrid Azure Ative Directory juntou-se a dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md).

Este artigo pressupõe que configuraste o [Azure Ative Directory híbrido juntou-se](hybrid-azuread-join-plan.md) a dispositivos para suportar os seguintes cenários:

- Acesso Condicional baseado em dispositivos
- [Roaming empresarial de configurações](../active-directory-windows-enterprise-state-roaming-overview.md)
- [Windows Hello para empresas](../active-directory-azureadjoin-passport-deployment.md)

Este documento fornece orientações de resolução de problemas para resolver potenciais problemas. 

Para windows 10 e Windows Server 2016, a adesão híbrida azure Ative Directy suporta a Atualização do Windows 10 de novembro de 2015 e acima.

## <a name="troubleshoot-join-failures"></a>Resolução de problemas junta-se a falhas

### <a name="step-1-retrieve-the-join-status"></a>Passo 1: Recuperar o estado da adesão 

**Para recuperar o estado de adesão:**

1. Abra um pedido de comando como administrador
2. Escreva `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Passo 2: Avaliar o estado da adesão 

Reveja os seguintes campos e certifique-se de que têm os valores esperados:

#### <a name="domainjoined--yes"></a>Domínio Unido: SIM  

Este campo indica se o dispositivo está ou não ligado a um Diretório Ativo no local. Se o valor for **NO,** o dispositivo não pode realizar uma adesão híbrida azure AD.  

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO  

Este campo indica se o dispositivo está registado com a AD Azure como um dispositivo pessoal (marcado como *Workplace Joined*). Este valor deve ser **NÃO** para um computador de domínio que também é híbrido Azure AD aderiu. Se o valor for **SIM**, foi adicionada uma conta de trabalho ou escola antes da conclusão da adesão híbrida azure AD. Neste caso, a conta é ignorada ao utilizar a versão De aniversário do Windows 10 (1607).

#### <a name="azureadjoined--yes"></a>AzureAdJoined: SIM  

Este campo indica se o dispositivo está unido. O valor será **SIM** se o dispositivo for um dispositivo azure ad ou um dispositivo híbrido Azure AD.
Se o valor for **NO,** a adesão à Azure AD ainda não está concluída. 

Proceda aos próximos passos para mais resolução de problemas.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Passo 3: Encontre a fase em que a adesão falhou e o código de erro

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e acima

Procure a subsecção 'Registo Prévio' na secção 'Dados de Diagnóstico' da saída do estado de adesão. Esta secção só é apresentada se o dispositivo for unido ao domínio e não conseguir aderir a Azure AD híbrido.
O campo 'Error Phase' denota a fase da falha de adesão enquanto o 'Código de Erro do Cliente' denota o código de erro da operação Join.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Versões antigas do Windows 10

Utilize os registos do Visualizador de Eventos para localizar a fase e o código de erro para as falhas de união.

1. Abra os registos do evento de registo do **dispositivo do utilizador** no espectador do evento. Localizado sob registo de **aplicações e serviços** > **Microsoft** > Registo de **dispositivos de utilizador** > **Windows**
2. Procure eventos com os seguintes eventosIDs 304, 305, 307.

![Evento de log de falha](./media/troubleshoot-hybrid-join-windows-current/1.png)

![Evento de log de falha](./media/troubleshoot-hybrid-join-windows-current/2.png)

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Passo 4: Verificar possíveis causas e resoluções das listas abaixo

#### <a name="pre-check-phase"></a>Fase de pré-verificação

Possíveis razões para o fracasso:

- O dispositivo não tem linha de visão para o controlador de domínio.
   - O dispositivo deve estar na rede interna da organização ou em VPN com linha de visão de rede para um controlador de domínio ative directory (AD) no local.

#### <a name="discover-phase"></a>Descubra fase

Possíveis razões para o fracasso:

- Ponto de ligação de serviço (SCP) objeto mal configurado/incapaz de ler objeto SCP a partir de DC.
   - É necessário um objeto SCP válido na floresta AD, a que o dispositivo pertence, que aponta para um nome de domínio verificado em Azure AD.
   - Os detalhes podem ser encontrados na secção [Configurar um ponto](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)de ligação de serviço .
- Falha em ligar e recolher os metadados de descoberta do ponto final da descoberta.
   - O dispositivo deverá poder aceder `https://enterpriseregistration.windows.net`, no contexto DO SISTEMA, a descobrir os pontos finais de registo e autorização. 
   - Se o ambiente no local necessitar de um proxy de saída, o administrador informático deve certificar-se de que a conta de computador do dispositivo é capaz de descobrir e autenticar silenciosamente o representante de saída.
- Falha na ligação ao ponto final do reino do utilizador e na realização da descoberta do reino. (Versão 1809 do Windows 10 e mais tarde apenas)
   - O dispositivo deve poder aceder `https://login.microsoftonline.com`, no contexto DO SISTEMA, para realizar a descoberta do reino para o domínio verificado e determinar o tipo de domínio (gerido/federado).
   - Se o ambiente no local necessitar de um proxy de saída, o administrador de TI deve assegurar que o contexto DO SISTEMA no dispositivo seja capaz de descobrir e autenticar silenciosamente o representante de saída.

**Códigos de erro comuns:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Razão: Incapaz de ler o objeto SCP e obter a informação do inquilino da AD Azure.
   - Resolução: Consulte a secção [Configure um ponto](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)de ligação de serviço .
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Razão: Falha na Descoberta Genérica. Não conseguiu obter os metadados de descoberta da DRS.
   - Resolução: Encontre o suberro abaixo para investigar mais aprofundadamente.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT** (0x801c001f/-2145648609)
   - Razão: A operação temprazo durante a realização do Discovery.
   - Resolução: Certifique-se de que `https://enterpriseregistration.windows.net` está acessível no contexto DO SISTEMA. Para mais informações, consulte os [requisitos](hybrid-azuread-join-managed-domains.md#prerequisites)de conectividade da rede da secção .
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Razão: Falha genérica da descoberta do reino. Não conseguiu determinar o tipo de domínio (gerido/federado) a partir de STS. 
   - Resolução: Encontre o suberro abaixo para investigar mais aprofundadamente.

**Códigos comuns de suberrogéo:**

Para encontrar o código de suberroctor para o código de erro de descoberta, utilize um dos seguintes métodos.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e acima

Procure 'DRS Discovery Test' na secção 'Dados de Diagnóstico' da saída do estado de adesão. Esta secção só é apresentada se o dispositivo for unido ao domínio e não conseguir aderir a Azure AD híbrido.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Versões antigas do Windows 10

Utilize os registos do Visualizador de Eventos para localizar a fase e código de erro para as falhas de união.

1. Abra os registos do evento de registo do **dispositivo do utilizador** no espectador do evento. Localizado sob registo de **aplicações e serviços** > **Microsoft** > Registo de **dispositivos de utilizador** > **Windows**
2. Procure eventos com os seguintes eventosIDs 201

![Evento de log de falha](./media/troubleshoot-hybrid-join-windows-current/5.png)

###### <a name="network-errors"></a>Erros de rede

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Razão: A ligação com o servidor não pôde ser estabelecida
   - Resolução: Garantir a conectividade da rede com os recursos necessários da Microsoft. Para obter mais informações, consulte os requisitos de [conectividade da rede.](hybrid-azuread-join-managed-domains.md#prerequisites)
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Razão: Intervalo geral da rede.
   - Resolução: Garantir a conectividade da rede com os recursos necessários da Microsoft. Para obter mais informações, consulte os requisitos de [conectividade da rede.](hybrid-azuread-join-managed-domains.md#prerequisites)
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Razão: A pilha de rede não foi capaz de descodificar a resposta do servidor.
   - Resolução: Certifique-se de que o proxy da rede não está a interferir e a modificar a resposta do servidor.

###### <a name="http-errors"></a>Erros HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Razão: Objeto SCP configurado com id de inquilino errado. Ou não foram encontradas assinaturas ativas no inquilino.
   - Resolução: Certifique-se de que o objeto SCP está configurado com o ID de inquilino Azure AD correto e subscrições ativas ou presente no inquilino.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Razão: HTTP 503 do servidor DRS.
   - Resolução: O servidor está atualmente indisponível. futuras tentativas de adesão provavelmente terão sucesso quando o servidor estiver de volta on-line.

###### <a name="other-errors"></a>Outros erros

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Razão: Resposta do servidor JSON não podia ser analisado. Provavelmente devido ao regresso de PROXY HTTP 200 com uma página auth HTML.
   - Resolução: Se o ambiente no local necessitar de um proxy de saída, o administrador de TI deve garantir que o contexto DO SISTEMA no dispositivo é capaz de descobrir e autenticar silenciosamente o representante de saída.

#### <a name="authentication-phase"></a>Fase de autenticação

Aplicável apenas para contas de domínio federados.

Razões para o fracasso:

- Incapaz de obter um token de acesso silenciosamente para recurso DRS.
   - Os dispositivos Windows 10 adquirem um símbolo auth do serviço da federação utilizando a Autenticação Integrada do Windows para um ponto final ativo do WS-Trust. Detalhes: [Configuração do Serviço da Federação](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Códigos de erro comuns:**

Utilize os registos do Visualizador de Eventos para localizar o código de erro, o código de erro, o código de erro do servidor e a mensagem de erro do servidor.

1. Abra os registos do evento de registo do **dispositivo do utilizador** no espectador do evento. Localizado sob registo de **aplicações e serviços** > **Microsoft** > Registo de **dispositivos de utilizador** > **Windows**
2. Procure eventos com o seguinte eventoID 305

![Evento de log de falha](./media/troubleshoot-hybrid-join-windows-current/3.png)

##### <a name="configuration-errors"></a>Erros de configuração

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Razão: O protocolo de autenticação não é WS-Trust.
   - Resolução: O fornecedor de identidade no local deve apoiar a WS-Trust 
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Razão: O serviço da federação no local não devolveu uma resposta XML.
   - Resolução: Certifique-se de que o ponto final do MEX está a devolver um XML válido. Certifique-se de que o proxy não está a interferir e a devolver respostas não xml.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Razão: Não foi possível descobrir o ponto final para a autenticação do nome de utilizador/palavra-passe.
   - Resolução: Verifique as definições do fornecedor de identidade no local. Certifique-se de que os pontos finais wS-Trust estão ativados e certifique-se de que a resposta MEX contém estes pontos finais corretos.

##### <a name="network-errors"></a>Erros de rede

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Razão: Intervalo geral da rede.
   - Resolução: Certifique-se de que `https://login.microsoftonline.com` está acessível no contexto DO SISTEMA. Certifique-se de que o fornecedor de identidade no local está acessível no contexto DO SISTEMA. Para obter mais informações, consulte os requisitos de [conectividade da rede.](hybrid-azuread-join-managed-domains.md#prerequisites)
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Razão: A ligação com o ponto final foi abortada.
   - Resolução: Tente depois de algum tempo ou tente juntar-se a partir de uma localização de rede estável alternativa.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Razão: O certificado Secure Sockets Layer (SSL) enviado pelo servidor não pôde ser validado.
   - Resolução: Verifique o tempo do cliente distorcido. Tente de novo após algum tempo ou tente juntar-se a partir de uma localização de rede estável alternativa. 
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Razão: A tentativa de ligação a `https://login.microsoftonline.com` falhou.
   - Resolução: Verifique a ligação da rede à `https://login.microsoftonline.com`.

##### <a name="other-errors"></a>Outros erros

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Razão: O símbolo da SAML do fornecedor de identidade no local não foi aceite pela Azure AD.
   - Resolução: Verifique as definições do servidor da federação. Procure o código de erro do servidor nos registos de autenticação.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Razão: Resposta do Server WS-Trust relatou exceção de falha e não conseguiu obter afirmação
   - Resolução: Verifique as definições do servidor da federação. Procure o código de erro do servidor nos registos de autenticação.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Razão: Recebeu um erro ao tentar obter o sinal de acesso do ponto final simbólico.
   - Resolução: Procure o erro subjacente no registo ADAL. 
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Razão: Falha geral da ADAL
   - Resolução: Procure o código de suberroou o código de erro do servidor a partir dos registos de autenticação.
    
#### <a name="join-phase"></a>Fase de adsiná-

Razões para o fracasso:

Encontre o tipo de registo e procure o código de erro na lista abaixo.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e acima

Procure a subsecção 'Registo Prévio' na secção 'Dados de Diagnóstico' da saída do estado de adesão. Esta secção só é apresentada se o dispositivo for unido ao domínio e não conseguir aderir a Azure AD híbrido.
O campo 'Tipo de registo' denota o tipo de adesão realizado.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Versões antigas do Windows 10

Utilize os registos do Visualizador de Eventos para localizar a fase e código de erro para as falhas de união.

1. Abra os registos do evento de registo do **dispositivo do utilizador** no espectador do evento. Localizado sob registo de **aplicações e serviços** > **Microsoft** > Registo de **dispositivos de utilizador** > **Windows**
2. Procure eventos com os seguintes eventosIDs 204

![Evento de log de falha](./media/troubleshoot-hybrid-join-windows-current/4.png)

##### <a name="http-errors-returned-from-drs-server"></a>Erros HTTP devolvidos do servidor DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Razão: Recebeu uma resposta de erro do DRS com ErrorCode: "ErroryError"
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Razão: Recebeu uma resposta de erro do DRS com ErrorCode: "AuthenticationError" e ErrorSubCode NÃO é "DeviceNotFound". 
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Razão: Recebeu uma resposta de erro do DRS com ErrorCode: "ErroryError"
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.

##### <a name="tpm-errors"></a>Erros tpm

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Razão: Operação TPM falhou ou foi inválida
   - Resolução: Provavelmente devido a uma má imagem de sysprep. Certifique-se de que a máquina a partir da qual a imagem de sysprep foi criada não é Azure AD aderiu, híbrido Azure AD juntou-se, ou Azure AD registrado.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Razão: Erro genérico do TPM. 
   - Resolução: Desative o TPM nos dispositivos com este erro. A versão 1809 do Windows 10 deteta automaticamente falhas de TPM e completa a adido híbrido Azure Sem utilizar o TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Razão: TPM no modo FIPS não suportado atualmente.
   - Resolução: Desative o TPM nos dispositivos com este erro. O Windows 1809 deteta automaticamente falhas de TPM e completa a adesão híbrida azure sem utilizar o TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Razão: TPM bloqueado.
   - Resolução: Erro transitório. Aguarde o período de arrefecimento. Juntar-se à tentativa depois de algum tempo deve ter sucesso. Mais informações podem ser encontradas no artigo [TPM fundamentos](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Erros de rede

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Razão: Tempo geral de rede para tentar registar o dispositivo na DRS
   - Resolução: Verifique a conectividade da rede para `https://enterpriseregistration.windows.net`.
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Razão: O nome ou endereço do servidor não poderia ser resolvido.
   - Resolução: Verifique a conectividade da rede para `https://enterpriseregistration.windows.net`. Certifique-se de que a resolução DNS para o nome de anfitrião é exata no n/w e no dispositivo.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Razão: A ligação com o servidor foi terminada anormalmente.
   - Resolução: Tente depois de algum tempo ou tente juntar-se a partir de uma localização de rede estável alternativa.

##### <a name="federated-join-server-errors"></a>Federado juntam-se a Erros de servidor

| Código de erro do servidor | Mensagem de erro do servidor | Possíveis razões | Resolução |
| --- | --- | --- | --- |
| Error do Diretório | O seu pedido é estrangulado temporariamente. Por favor, tente depois de 300 segundos. | Erro esperado. Possivelmente devido a vários pedidos de registo em rápida sucessão. | Volte a juntar-se após o período de arrefecimento |

##### <a name="sync-join-server-errors"></a>Sync juntar erros de servidor

| Código de erro do servidor | Mensagem de erro do servidor | Possíveis razões | Resolução |
| --- | --- | --- | --- |
| Error do Diretório | AADSTS90002: Inquilino <UUID> não encontrado. Este erro pode acontecer se não houver subscrições ativas para o inquilino. Consulte o seu administrador de subscrição. | Id do inquilino no objeto SCP é incorreto | Certifique-se de que o objeto SCP está configurado com o ID de inquilino Azure AD correto e subscrições ativas e presente no inquilino. |
| Error do Diretório | O objeto do dispositivo pelo ID dado não é encontrado. | Erro esperado para sincronização aderir. O objeto do dispositivo não se sincronizou de AD para Azure AD | Aguarde que a sincronização Azure AD Connect esteja concluída e a próxima tentativa de adesão após a conclusão da sincronização resolverá o problema |
| Erro de autenticação | A verificação do SID do computador-alvo | O certificado do dispositivo Azure AD não corresponde ao certificado utilizado para assinar a bolha durante a sincronização. Este erro normalmente significa que a sincronização ainda não está concluída. |  Aguarde que a sincronização Azure AD Connect esteja concluída e a próxima tentativa de adesão após a conclusão da sincronização resolverá o problema |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Passo 5: Recolher registos e contactar o Suporte da Microsoft

Obtenha roteiros públicos aqui: [https://1drv.ms/u/s! AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ]( https://1drv.ms/u/s!AkyTjQ17vtfagYkZ6VJzPg78e3o7PQ)

1. Abra um pedido de comando administrativo e corra `start_ngc_tracing_public.cmd`.
2. Execute os passos para reproduzir a questão.
3. Pare de executar o script de registo executando `stop_ngc_tracing_public.cmd`.
4. Feche e envie os registos sob `%SYSTEMDRIVE%\TraceDJPP\*` para análise.

## <a name="troubleshoot-post-join-issues"></a>Problemas de pós-adesão

### <a name="retrieve-the-join-status"></a>Recuperar o estado de adesão 

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: YES e AzureADPrt: SIM
  
Estes campos indicam se o utilizador autenticou com sucesso à Azure AD ao iniciar a sessão no dispositivo. Se os valores forem **NÃO,** pode ser devido:

- Má chave de armazenamento no TPM associado ao dispositivo no momento do registo (verifique o KeySignTest enquanto está em funcionamento elevado).
- Id de login alternativo
- HTTP Proxy não encontrado

## <a name="known-issues"></a>Problemas conhecidos
- Em Definições -> Contas -> Trabalho de Acesso ou Escola, dispositivos híbridos azure ad podem apresentar duas contas diferentes, uma para Azure AD e outra para a AD no local, quando ligadas a hotspots móveis ou redes Wi-Fi externas. Este é apenas um problema de UI e não tem qualquer impacto na funcionalidade. 
 
## <a name="next-steps"></a>Passos seguintes

Continue os dispositivos de resolução de [problemas utilizando o comando dsregcmd](troubleshoot-device-dsregcmd.md)

Para perguntas, consulte as FAQ de [gestão de dispositivos](faq.md)
