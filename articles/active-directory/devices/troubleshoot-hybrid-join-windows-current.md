---
title: Resolver problemas de dispositivos associados ao Azure Active Directory híbrido
description: O Azure Ative Directy híbrido de resolução de problemas juntou-se aos dispositivos Windows 10 e Windows Server 2016.
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
ms.custom: has-adal-ref
ms.openlocfilehash: 3fd837a4c547b227147752b03b6a7312c733a5ae
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057019"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Resolver problemas de dispositivos associados ao Azure Active Directory híbrido

O conteúdo deste artigo é aplicável aos dispositivos que executam o Windows 10 ou o Windows Server 2016.

Para outros clientes windows, consulte o artigo [Resolução de problemas híbrido Azure Ative Directory juntou dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md).

Este artigo pressupõe que [configuraste dispositivos híbridos Azure Ative Directory](hybrid-azuread-join-plan.md) para suportar os seguintes cenários:

- Acesso Condicional com base em dispositivos
- [Roaming empresarial de configurações](./enterprise-state-roaming-overview.md)
- [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

Este documento fornece orientações para a resolução de problemas para resolver potenciais problemas.

Para o Windows 10 e Windows Server 2016, o Azure Ative Directy híbrido junta-se aos suportes da Atualização do Windows 10 de novembro de 2015 e acima.

## <a name="troubleshoot-join-failures"></a>Resolução de problemas junta falhas

### <a name="step-1-retrieve-the-join-status"></a>Passo 1: Recuperar o estado de junção

**Para recuperar o estado de união:**

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

### <a name="step-2-evaluate-the-join-status"></a>Passo 2: Avaliar o estado de união

Reveja os seguintes campos e certifique-se de que têm os valores esperados:

#### <a name="domainjoined--yes"></a>DomainJoined : SIM

Este campo indica se o dispositivo está ou não ligado a um Ative Directory no local. Se o valor for **NÃO,** o dispositivo não pode executar uma junção AD híbrida Azure.

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO

Este campo indica se o dispositivo está registado no Azure AD como um dispositivo pessoal (marcado como *Workplace Joined*). Este valor deve ser **NO** para um computador de domínio que também é híbrido Azure AD. Se o valor for **SIM,** foi adicionada uma conta de trabalho ou escola antes da conclusão da ad azure híbrida. Neste caso, a conta é ignorada quando se utiliza a versão 1607 ou posterior do Windows 10.

#### <a name="azureadjoined--yes"></a>AzureAdJoined : SIM

Este campo indica se o dispositivo está unido. O valor será **SIM** se o dispositivo for um dispositivo azure AD ou um dispositivo híbrido Azure AD.
Se o valor for **NÃO,** a junção ao Azure AD ainda não está concluída.

Avance para os próximos passos para uma nova resolução de problemas.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Passo 3: Encontrar a fase em que a junção falhou e o código de erro

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e superior

Procure a subsecção 'Registo Prévio' na secção 'Dados de Diagnóstico' da saída do estado de ligação. Esta secção só é apresentada se o dispositivo estiver unido ao domínio e não for capaz de juntar a AD híbrida.
O campo 'Fase de Erro' denota a fase da falha de junção enquanto o 'Código de Erro do Cliente' denota o código de erro da operação ''Junção'.

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

Utilize registos do Visualizador de Eventos para localizar o código de fase e erro para as falhas de junção.

1. Abra os registos de eventos **de registo do dispositivo do utilizador** no visualizador do evento. Localizado sob **aplicações e serviços registam o** registo do dispositivo do  >    >    >  **utilizador** do Microsoft Windows
2. Procure eventos com os seguintes eventosIDs 304, 305, 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Screenshot do espectador do evento. É selecionado um evento com O I D 304, e as suas informações são exibidas, com o código de erro e a fase realçadas." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Screenshot do espectador do evento. É visível um evento com O I D 305 e a sua informação é exibida, com o código de erro realçado." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Passo 4: Verificar possíveis causas e resoluções das listas abaixo

#### <a name="pre-check-phase"></a>Fase de pré-verificação

Possíveis razões para o fracasso:

- O dispositivo não tem linha de visão para o controlador de domínio.
   - O dispositivo deve estar na rede interna da organização ou em VPN com linha de visão de rede para um controlador de domínio Ativo (AD) no local.

#### <a name="discover-phase"></a>Descobrir fase

Possíveis razões para o fracasso:

- Ponto de ligação de serviço (SCP) objeto configurado/incapaz de ler o objeto SCP de DC.
   - É necessário um objeto SCP válido na floresta AD, a que pertence o dispositivo, que aponta para um nome de domínio verificado em Azure AD.
   - Os detalhes podem ser encontrados na secção [Configurar um Ponto de Ligação de Serviço](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Falha na ligação e na obtenção dos metadados da descoberta do ponto final da descoberta.
   - O dispositivo deverá poder `https://enterpriseregistration.windows.net` aceder, no contexto SYSTEM, para descobrir os pontos finais de registo e autorização.
   - Se o ambiente no local necessitar de um representante de saída, o administrador de TI deve certificar-se de que a conta de computador do dispositivo é capaz de descobrir e autenticar silenciosamente para o representante de saída.
- Falha na ligação ao ponto final do reino do utilizador e realização de descoberta de reinos. (Versão 1809 do Windows 10 e mais tarde apenas)
   - O dispositivo deve poder `https://login.microsoftonline.com` aceder, no contexto SYSTEM, para realizar a descoberta do reino para o domínio verificado e determinar o tipo de domínio (gerido/federado).
   - Se o ambiente no local necessitar de um representante de saída, o administrador de TI deve garantir que o contexto SYSTEM do dispositivo seja capaz de descobrir e autenticar silenciosamente para o representante de saída.

**Códigos de erro comuns:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Razão: Incapaz de ler o objeto SCP e obter a informação do inquilino da AZure AD.
   - Resolução: Consulte a secção [Configurar um Ponto de Ligação de Serviço](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Razão: Falha genérica da Descoberta. Falhou em obter os metadados de descoberta da DRS.
   - Resolução: Encontre o suberror abaixo para investigar mais aprofundadamente.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Razão: Operação cronometrada durante a realização do Discovery.
   - Resolução: Certifique-se de que `https://enterpriseregistration.windows.net` está acessível no contexto SYSTEM. Para obter mais informações, consulte os [requisitos de conectividade da rede](hybrid-azuread-join-managed-domains.md#prerequisites)de secção .
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Razão: Falha genérica da Descoberta do Reino. Não conseguiu determinar o tipo de domínio (gerido/federado) a partir de STS.
   - Resolução: Encontre o suberror abaixo para investigar mais aprofundadamente.

**Códigos suberrtores comuns:**

Para encontrar o código do suberror para o código de erro de descoberta, utilize um dos seguintes métodos.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e superior

Procure "DRS Discovery Test" na secção 'Dados de Diagnóstico' da saída do estado de ligação. Esta secção só é apresentada se o dispositivo estiver unido ao domínio e não for capaz de juntar a AD híbrida.

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

Utilize registos do Visualizador de Eventos para localizar a fase e o código de erro para as falhas de junção.

1. Abra os registos de eventos **de registo do dispositivo do utilizador** no visualizador do evento. Localizado sob **aplicações e serviços registam o** registo do dispositivo do  >    >    >  **utilizador** do Microsoft Windows
2. Procure eventos com os seguintes eventosIDs 201

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Screenshot do espectador do evento. É selecionado um evento com o I D 201 e as suas informações são apresentadas, com o código de erro realçado." border="false":::

###### <a name="network-errors"></a>Erros de rede

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Razão: A ligação com o servidor não foi possível estabelecer
   - Resolução: Garantir a conectividade da rede com os recursos necessários da Microsoft. Para obter mais informações, consulte [os requisitos de conectividade da Rede.](hybrid-azuread-join-managed-domains.md#prerequisites)
- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Razão: Tempo limite geral de rede.
   - Resolução: Garantir a conectividade da rede com os recursos necessários da Microsoft. Para obter mais informações, consulte [os requisitos de conectividade da Rede.](hybrid-azuread-join-managed-domains.md#prerequisites)
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Razão: A pilha de rede não foi capaz de descodificar a resposta do servidor.
   - Resolução: Certifique-se de que o proxy da rede não está a interferir e a modificar a resposta do servidor.

###### <a name="http-errors"></a>Erros de HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Razão: Objeto SCP configurado com identificação errada do inquilino. Ou não foram encontradas assinaturas ativas no inquilino.
   - Resolução: Certifique-se de que o objeto SCP está configurado com o ID correto do inquilino Azure AD e com assinaturas ativas ou presentes no arrendatário.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Razão: HTTP 503 do servidor DRS.
   - Resolução: O servidor encontra-se atualmente indisponível. futuras tentativas de junção provavelmente terão sucesso uma vez que o servidor está novamente on-line.

###### <a name="other-errors"></a>Outros erros

- **E_INVALIDDATA** (0x8007000d/-2147024883)
   - Razão: Resposta do servidor JSON não podia ser analisada. Provavelmente devido a procuração de volta HTTP 200 com uma página de auth HTML.
   - Resolução: Se o ambiente no local necessitar de um representante de saída, o administrador de TI deve garantir que o contexto SYSTEM do dispositivo seja capaz de descobrir e autenticar silenciosamente para o representante de saída.

#### <a name="authentication-phase"></a>Fase de autenticação

Aplicável apenas para contas de domínio federado.

Razões para falhar:

- Não é possível obter um token de acesso silenciosamente para recurso DRS.
   - Os dispositivos Windows 10 adquirem um símbolo auth do serviço da federação utilizando a Autenticação Integrada do Windows para um ponto final WS-Trust ativo. Detalhes: [Configuração do Serviço da Federação](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Códigos de erro comuns:**

Utilize registos do Visualizador de Eventos para localizar o código de erro, o código do suberror, o código de erro do servidor e a mensagem de erro do servidor.

1. Abra os registos de eventos **de registo do dispositivo do utilizador** no visualizador do evento. Localizado sob **aplicações e serviços registam o** registo do dispositivo do  >    >    >  **utilizador** do Microsoft Windows
2. Procure eventos com o seguinte eventoID 305

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Screenshot do espectador do evento. Um evento com O I D 305 é visível. As suas informações são apresentadas, com os códigos de erro A D A L e o estado realçado." border="false":::

##### <a name="configuration-errors"></a>Erros de configuração

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Razão: O protocolo de autenticação não é WS-Trust.
   - Resolução: O fornecedor de identidade no local deve apoiar WS-Trust
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Razão: O serviço da federação no local não devolveu uma resposta XML.
   - Resolução: Certifique-se de que o ponto final MEX está a devolver um XML válido. Certifique-se de que o proxy não está a interferir e a devolver respostas não xml.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Razão: Não foi possível descobrir o ponto final para a autenticação do nome de utilizador/palavra-passe.
   - Resolução: Verifique as definições do fornecedor de identidade no local. Certifique-se de que os WS-Trust pontos finais estão ativados e certifique-se de que a resposta MEX contém estes pontos finais corretos.

##### <a name="network-errors"></a>Erros de rede

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Razão: Tempo limite geral de rede.
   - Resolução: Certifique-se de que `https://login.microsoftonline.com` está acessível no contexto SYSTEM. Certifique-se de que o fornecedor de identidade no local está acessível no contexto SYSTEM. Para obter mais informações, consulte [os requisitos de conectividade da Rede.](hybrid-azuread-join-managed-domains.md#prerequisites)
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Razão: A ligação com o auth endpoint foi abortada.
   - Resolução: Relemca após algum tempo ou tente juntar-se a partir de uma localização de rede estável alternativa.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Razão: A Segurança da Camada de Transporte (TLS), anteriormente conhecida como Camada de Tomadas Seguras (SSL), certificado enviado pelo servidor não pôde ser validado.
   - Resolução: Verifique o desvio do tempo do cliente. Reforce depois de algum tempo ou tente juntar-se a partir de uma localização de rede estável alternativa.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Razão: A tentativa de ligação ao `https://login.microsoftonline.com` fracasso.
   - Resolução: Verifique a ligação da rede a `https://login.microsoftonline.com` .

##### <a name="other-errors"></a>Outros erros

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Razão: O sinal DE SAML do fornecedor de identidade no local não foi aceite pela Azure AD.
   - Resolução: Verifique as definições do servidor da federação. Procure o código de erro do servidor nos registos de autenticação.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Reason: Servidor WS-Trust resposta reportada exceção de falha e não conseguiu obter afirmação
   - Resolução: Verifique as definições do servidor da federação. Procure o código de erro do servidor nos registos de autenticação.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Razão: Recebeu um erro ao tentar obter o token de acesso a partir do ponto final do token.
   - Resolução: Procure o erro subjacente no registo ADAL.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Razão: Falha geral da ADAL
   - Resolução: Procure o código do suberror ou código de erro do servidor a partir dos registos de autenticação.

#### <a name="join-phase"></a>Aderir fase

Razões para falhar:

Encontre o tipo de registo e procure o código de erro da lista abaixo.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 e superior

Procure a subsecção 'Registo Prévio' na secção 'Dados de Diagnóstico' da saída do estado de ligação. Esta secção só é apresentada se o dispositivo estiver unido ao domínio e não for capaz de juntar a AD híbrida.
O campo 'Tipo de Registo' denota o tipo de junção realizada.

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

Utilize registos do Visualizador de Eventos para localizar a fase e o código de erro para as falhas de junção.

1. Abra os registos de eventos **de registo do dispositivo do utilizador** no visualizador do evento. Localizado sob **aplicações e serviços registam o** registo do dispositivo do  >    >    >  **utilizador** do Microsoft Windows
2. Procure eventos com os seguintes eventosIDs 204

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Screenshot do espectador do evento. São apresentadas informações sobre um evento com OD 204, com o código de erro, o estado de H T T P e a mensagem realçada." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>ERROS HTTP devolvidos do servidor DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Razão: Recebeu uma resposta de erro da DRS com ErrorCode: "DirectoryError"
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Razão: Recebeu uma resposta de erro do DRS com ErrorCode: "AuthenticationError" e ErrorSubCode não é "DeviceNotFound".
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Razão: Recebeu uma resposta de erro da DRS com ErrorCode: "DirectoryError"
   - Resolução: Consulte o código de erro do servidor por possíveis razões e resoluções.

##### <a name="tpm-errors"></a>Erros do TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Razão: A operação TPM falhou ou foi inválida
   - Resolução: Provavelmente devido a uma má imagem de sysprep. Certifique-se de que a máquina a partir da qual a imagem sysprep foi criada não é a ad Azure, a ad híbrido Azure ad, ou Azure AD registrado.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Razão: Erro genérico do TPM.
   - Resolução: Desative o TPM em dispositivos com este erro. O Windows 10 versão 1809 e superior deteta automaticamente falhas de TPM e completa a união híbrida Azure AD sem utilizar o TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Razão: TPM no modo FIPS não suportado atualmente.
   - Resolução: Desative o TPM em dispositivos com este erro. O Windows 1809 deteta automaticamente falhas de TPM e completa a união híbrida Azure AD sem utilizar o TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Razão: TPM bloqueado fora.
   - Resolução: Erro transitório. Aguarde o período de arrefecimento. Junte-se à tentativa depois de algum tempo deve ter sucesso. Mais Informações podem ser encontradas no artigo [Fundamentos TPM](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Erros de rede

- **WININET_E_TIMEOUT** (0x80072ee2/-2147012894)
   - Razão: Tempo de 30 minutos geral de rede tentando registar o dispositivo na DRS
   - Resolução: Verifique a conectividade da rede para `https://enterpriseregistration.windows.net` .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Razão: O nome ou endereço do servidor não pôde ser resolvido.
   - Resolução: Verifique a conectividade da rede para `https://enterpriseregistration.windows.net` . Certifique-se de que a resolução DENS para o nome de anfitrião está correta na n/w e no dispositivo.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Razão: A ligação com o servidor foi terminada anormalmente.
   - Resolução: Relemca após algum tempo ou tente juntar-se a partir de uma localização de rede estável alternativa.

##### <a name="other-errors"></a>Outros Erros

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Razão: O EventID 220 está presente nos registos de eventos do User Device. O Windows não pode aceder ao objeto do computador no Ative Directory. Um código de erro do Windows pode ser incluído no caso. Para códigos de erro ERROR_NO_SUCH_LOGON_SESSION (1312) e ERROR_NO_SUCH_USER (1317), estes estão relacionados com problemas de replicação em AD no local.
   - Resolução: Problemas de replicação de resolução de problemas em AD. Problemas de replicação podem ser transitórios e podem ir muito longe após um período de tempo.

##### <a name="federated-join-server-errors"></a>Federado juntam-se a erros de servidor

| Código de erro do servidor | Mensagem de erro do servidor | Motivos possíveis | Resolução |
| --- | --- | --- | --- |
| Diretório | O seu pedido foi temporariamente estrangulado. Por favor, tente depois de 300 segundos. | Erro esperado. Possivelmente devido a fazer vários pedidos de registo em rápida sucessão. | Recandidutar após o período de arrefecimento |

##### <a name="sync-join-server-errors"></a>Sync juntar-se a erros do servidor

| Código de erro do servidor | Mensagem de erro do servidor | Motivos possíveis | Resolução |
| --- | --- | --- | --- |
| Diretório | AADSTS90002: Inquilino <UUID> não encontrado. Este erro pode ocorrer se não houver subscrições ativas para o arrendatário. Consulte o administrador de subscrição. | ID do inquilino em objeto SCP está incorreto | Certifique-se de que o objeto SCP está configurado com o ID do inquilino Azure AD correto e com assinaturas ativas e presentes no arrendatário. |
| Diretório | O objeto do dispositivo pelo ID dado não é encontrado. | Erro esperado para sincronização. O objeto do dispositivo não sincronizou de AD a AZure AD | Aguarde que a sincronização do Azure AD Connect esteja concluída e a próxima tentativa de junção após a conclusão da sincronização resolverá o problema |
| AutenticaçãoError | A verificação do SID do computador-alvo | O certificado no dispositivo AD Azure não corresponde ao certificado utilizado para assinar a bolha durante a junção de sincronização. Este erro normalmente significa que a sincronização ainda não está concluída. |  Aguarde que a sincronização do Azure AD Connect esteja concluída e a próxima tentativa de junção após a conclusão da sincronização resolverá o problema |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Passo 5: Recolher registos e contactar o Microsoft Support

Descarregue o ficheiro Auth.zip a partir de [https://github.com/CSS-Identity/DRS/tree/main/Auth](https://github.com/CSS-Identity/DRS/tree/main/Auth)

1. Desaperte os ficheiros e rebatize os ficheiros incluídos **start-auth.txt** e **stop-auth.txt** para **start-auth.cmd** e **stop-auth.cmd**.
1. A partir de um pedido de comando elevado, executar **start-auth.cmd**.
1. Utilize a Conta Switch para alternar para outra sessão com o utilizador problemático.
1. Reproduza o problema.
1. Utilize a Conta Switch para alternar de volta para a sessão de administração que executa o rastreio.
1. A partir de um pedido de comando elevado, executar **stop-auth.cmd**.
1. Zip e envie a pasta **Authlogs** da pasta de onde os scripts foram executados.

## <a name="troubleshoot-post-join-issues"></a>Problemas pós-adere

### <a name="retrieve-the-join-status"></a>Recuperar o estado de junção

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: SIM e AzureADPrt: SIM

Estes campos indicam se o utilizador autenticou com sucesso a Azure AD ao iniciar sessão no dispositivo.
Se os valores forem **NÃO,** pode ser devido:

- Chave de armazenamento estragada no TPM associado ao dispositivo no momento do registo (verifique o KeySignTest enquanto está em funcionamento elevado).
- ID de login alternativo
- HTTP Proxy não encontrado

## <a name="known-issues"></a>Problemas conhecidos
- Em Definições -> Contas -> Access Work ou Escola, os dispositivos híbridos Azure AD podem apresentar duas contas diferentes, uma para AZure AD e outra para AD no local, quando ligadas a hotspots móveis ou redes Wi-Fi externas. Este é apenas um problema de UI e não tem qualquer impacto na funcionalidade.

## <a name="next-steps"></a>Passos seguintes

Continue [a resolução de problemas utilizando o comando dsregcmd](troubleshoot-device-dsregcmd.md)

Para dúvidas, consulte a [faq de gestão](faq.md) de dispositivos
