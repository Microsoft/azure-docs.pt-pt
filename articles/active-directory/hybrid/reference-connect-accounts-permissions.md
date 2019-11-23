---
title: 'Azure AD Connect: contas e permissões | Microsoft Docs'
description: Este tópico descreve as contas usadas e criadas e as permissões necessárias.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72ae1301be4a3a3c086961aae72fb9eeb12aeda2
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960238"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: contas e permissões

## <a name="accounts-used-for-azure-ad-connect"></a>Contas usadas para Azure AD Connect

![Visão geral das contas](media/reference-connect-accounts-permissions/account5.png)

O Azure AD Connect usa 3 contas para sincronizar informações do Active Directory do local ou do Windows Server para Azure Active Directory.  Essas contas são:

- **Conta do conector de AD DS**: usada para ler/gravar informações no Windows Server Active Directory

- **Conta de serviço AdSync**: usada para executar o serviço de sincronização e acessar o banco de dados SQL

- **Conta do conector do Azure ad**: usada para gravar informações no Azure AD

Além dessas três contas usadas para executar o Azure AD Connect, você também precisará das seguintes contas adicionais para instalar o Azure AD Connect.  Nomeadamente:

- **Conta de administrador local**: o administrador que está instalando Azure ad Connect e quem tem permissões de administrador local no computador.

- **AD DS conta de administrador corporativo**: opcionalmente, usada para criar a "conta do conector de AD DS" acima.

- **Conta de administrador global do Azure ad**: usada para criar a conta do conector do Azure AD e configurar o Azure AD.

- **Conta SA do SQL (opcional)** : usada para criar o banco de dados AdSync ao usar a versão completa do SQL Server.  Esse SQL Server pode ser local ou remoto para a instalação do Azure AD Connect.  Essa conta pode ser a mesma conta que o administrador corporativo.  O provisionamento do banco de dados agora pode ser executado fora da banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário do banco de dados.  Para obter informações sobre isso, consulte [instalar Azure ad Connect usando permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> A partir da compilação 1.4. # # #. # não há mais suporte para usar um administrador corporativo ou uma conta de administrador de domínio como a conta do conector de AD DS.  Se você tentar inserir uma conta que seja um administrador corporativo ou administrador de domínio ao especificar **usar conta existente**, receberá um erro.

> [!NOTE]
> Há suporte para gerenciar as contas administrativas usadas em Azure AD Connect de uma floresta administrativa ESAE (também conhecida como "floresta vermelha").
> As florestas administrativas dedicadas permitem que as organizações hospedem contas administrativas, estações de trabalho e grupos em um ambiente que tenha controles de segurança mais fortes do que o ambiente de produção.
> Para saber mais sobre florestas administrativas dedicadas, consulte a [abordagem de design de floresta administrativa do Esae](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach)
>>>>>>> e683a61b0ed62ae739941410f658a127534e2481

> [!NOTE]
> A função de administrador global não é necessária após a configuração inicial e a única conta necessária será a conta de função de **contas de sincronização de diretórios** . Isso não necssarily significa que você desejará apenas remover a conta com a função de administrador global. É melhor alterar a função para uma função menos poderosa, pois a remoção total da conta pode apresentar problemas se você precisar executar novamente o assistente novamente. Ao reduzir o privilégio da função, você sempre poderá reelevar os privilégios se precisar utilizar o assistente de Azure AD Connect novamente. 

## <a name="installing-azure-ad-connect"></a>Instalando o Azure AD Connect
O assistente de instalação do Azure AD Connect oferece dois caminhos diferentes:

* Nas configurações expressas, o assistente requer mais privilégios.  Isso é para que ele possa definir sua configuração facilmente, sem exigir que você crie usuários ou Configure permissões.
* Em configurações personalizadas, o assistente oferece mais opções e opções. No entanto, há algumas situações em que você precisa garantir que tenha as permissões corretas por conta própria.



## <a name="express-settings-installation"></a>Instalação de configurações expressas
Nas configurações expressas, o assistente de instalação solicita o seguinte:

  - AD DS de credenciais de administrador corporativo
  - Credenciais de administrador global do Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>AD DS credenciais de administrador corporativo
A conta de administrador corporativo AD DS é usada para configurar sua Active Directory local. Essas credenciais são usadas apenas durante a instalação e não são usadas após a conclusão da instalação. O administrador corporativo, não o administrador de domínio deve ter certeza de que as permissões no Active Directory podem ser definidas em todos os domínios.

Se você estiver atualizando do DirSync, as credenciais do AD DS Enterprise Admins serão usadas para redefinir a senha para a conta usada pelo DirSync. Você também precisa de credenciais de administrador global do Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Credenciais de administrador global do Azure AD
Essas credenciais são usadas apenas durante a instalação e não são usadas após a conclusão da instalação. Ele é usado para criar a conta do conector do Azure AD usada para sincronizar alterações no Azure AD. A conta também habilita a sincronização como um recurso no Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Permissões necessárias da conta do conector do AD DS para as configurações expressas
A conta do conector de AD DS é criada para leitura e gravação no AD do Windows Server e tem as seguintes permissões quando criadas pelas configurações expressas:

| Permissão | Utilizado para |
| --- | --- |
| <li>Replicar alterações de diretório</li><li>Replicar todas as alterações de diretório |Sincronização de hash de senha |
| Ler/gravar todas as propriedades usuário |Importar e trocar híbrido |
| Ler/gravar todas as propriedades iNetOrgPerson |Importar e trocar híbrido |
| Ler/gravar todas as propriedades grupo |Importar e trocar híbrido |
| Ler/gravar todas as propriedades contato |Importar e trocar híbrido |
| Repor palavra-passe |Preparação para habilitar o Write-back de senha |

### <a name="express-installation-wizard-summary"></a>Resumo do assistente de instalação expressa

![Instalação expressa](./media/reference-connect-accounts-permissions/express.png)

Veja a seguir um resumo das páginas do assistente de instalação expressa, das credenciais coletadas e de como elas são usadas.

| Página do assistente | Credenciais coletadas | Permissões necessárias | Usado para |
| --- | --- | --- | --- |
| N/D |Usuário executando o assistente de instalação |Administrador do servidor local |<li>Cria a conta de serviço ADSync que é usada como para executar o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório do Azure AD |Função de administrador global no Azure AD |<li>Habilitando a sincronização no diretório do AD do Azure.</li>  <li>Criação da conta do conector do AD do Azure que é usada para operações de sincronização em andamento no Azure AD.</li> |
| Ligar ao AD DS |Credenciais de Active Directory local |Membro do grupo de administradores de empresa (EA) no Active Directory |<li>Cria a conta do conector de AD DS no Active Directory e concede permissões a ela. Essa conta criada é usada para ler e gravar informações de diretório durante a sincronização.</li> |


## <a name="custom-installation-settings"></a>Configurações de instalação personalizadas

Com a instalação de configurações personalizadas, o assistente oferece mais opções e opções. 

### <a name="custom-installation-wizard-summary"></a>Resumo do assistente de instalação personalizada

Veja a seguir um resumo das páginas do assistente de instalação personalizada, as credenciais coletadas e para que elas são usadas.

![Instalação expressa](./media/reference-connect-accounts-permissions/customize.png)

| Página do assistente | Credenciais coletadas | Permissões necessárias | Usado para |
| --- | --- | --- | --- |
| N/D |Usuário executando o assistente de instalação |<li>Administrador do servidor local</li><li>Se estiver usando uma SQL Server completa, o usuário deverá ser o administrador do sistema (SA) no SQL</li> |Por padrão, o cria a conta local que é usada como a conta de serviço do mecanismo de sincronização. A conta é criada somente quando o administrador não especifica uma conta específica. |
| Instalar serviços de sincronização, opção de conta de serviço |Credenciais de conta de usuário local ou do AD |Usuário, as permissões são concedidas pelo assistente de instalação |Se o administrador especificar uma conta, essa conta será usada como a conta de serviço para o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório do Azure AD |Função de administrador global no Azure AD |<li>Habilitando a sincronização no diretório do AD do Azure.</li>  <li>Criação da conta do conector do AD do Azure que é usada para operações de sincronização em andamento no Azure AD.</li> |
| Ligar os diretórios |As credenciais de Active Directory local para cada floresta que está conectada ao Azure AD |As permissões dependem de quais recursos você habilita e podem ser encontradas em criar a conta do conector de AD DS |Essa conta é usada para ler e gravar informações de diretório durante a sincronização. |
| Servidores do AD FS |Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de entrada do usuário que executa o assistente não são suficientes para se conectar |Administrador de domínio |Instalação e configuração da função de servidor AD FS. |
| Servidores proxy de aplicativo Web |Para cada servidor na lista, o assistente coleta credenciais quando as credenciais de entrada do usuário que executa o assistente não são suficientes para se conectar |Administrador local no computador de destino |Instalação e configuração da função de servidor WAP. |
| Credenciais de confiança de proxy |Credenciais de confiança do serviço de Federação (as credenciais que o proxy usa para se registrar para um certificado de confiança do FS |Conta de domínio que é um administrador local do servidor de AD FS |Registro inicial do certificado de confiança FS-WAP. |
| AD FS página conta de serviço, "usar uma opção de conta de usuário de domínio" |Credenciais da conta de usuário do AD |Usuário de domínio |A conta de usuário do Azure AD cujas credenciais são fornecidas é usada como a conta de entrada do serviço de AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Criar a conta do conector de AD DS

>[!IMPORTANT]
>Um novo módulo do PowerShell chamado ADSyncConfig. psm1 foi introduzido com Build **1.1.880.0** (lançado em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões de Active Directory corretas para a conta do conector de AD DS do Azure.
>
>Para obter mais informações, consulte a [permissão Azure ad Connect: configurar a conta do conector de AD DS](how-to-connect-configure-ad-ds-connector-account.md)

A conta que você especificar na página **conectar seus diretórios** deve estar presente no Active Directory antes da instalação.  Azure AD Connect versão 1.1.524.0 e posterior tem a opção de permitir que o assistente de Azure AD Connect crie a **conta do conector de AD DS** usada para se conectar ao Active Directory.  

Ele também deve ter as permissões necessárias concedidas. O assistente de instalação não verifica as permissões e os problemas só são encontrados durante a sincronização.

As permissões que você precisa dependem dos recursos opcionais que você habilita. Se você tiver vários domínios, as permissões deverão ser concedidas para todos os domínios na floresta. Se você não habilitar nenhum desses recursos, as permissões de **usuário de domínio** padrão serão suficientes.

| Funcionalidade | Permissões |
| --- | --- |
| recurso ms-DS-ConsistencyGuid |Permissões de gravação para o atributo ms-DS-ConsistencyGuid documentado em [conceitos de design – usando MS-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de senha |<li>Replicar alterações de diretório</li>  <li>Replicar todas as alterações de diretório |
| Implantação híbrida do Exchange |Permissões de gravação para os atributos documentados no [write-back híbrido do Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para usuários, grupos e contatos. |
| Pasta pública de email do Exchange |Permissões de leitura para os atributos documentados na [pasta pública do Exchange mail](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Repetição de escrita de palavras-passe |Permissões de gravação para os atributos documentados em [introdução ao gerenciamento de senhas](../authentication/howto-sspr-writeback.md) para usuários. |
| Repetição de escrita do dispositivo |Permissões concedidas com um script do PowerShell, conforme descrito em [write-back do dispositivo](how-to-connect-device-writeback.md). |
| Repetição de escrita do grupo |Permite que você write-back de **grupos do Office 365** em uma floresta com o Exchange instalado.  Para obter mais informações, consulte [Group write-back](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Atualizar
Ao atualizar de uma versão do Azure AD Connect para uma nova versão, você precisará das seguintes permissões:

>[!IMPORTANT]
>A partir do Build 1.1.484, Azure AD Connect introduziu um bug de regressão que exige permissões sysadmin para atualizar o banco de dados SQL.  Esse bug é corrigido no Build 1.1.647.  Se estiver atualizando para essa compilação, você precisará de permissões de sysadmin.  As permissões de dbo não são suficientes.  Se você tentar atualizar Azure AD Connect sem ter permissões de sysadmin, a atualização falhará e Azure AD Connect deixará de funcionar corretamente mais tarde.  A Microsoft está ciente disso e está trabalhando para corrigir isso.


| Beneficiário | Permissões necessárias | Utilizado para |
| --- | --- | --- |
| Usuário executando o assistente de instalação |Administrador do servidor local |Atualizar binários. |
| Usuário executando o assistente de instalação |Membro de ADSyncAdmins |Faça alterações nas regras de sincronização e outras configurações. |
| Usuário executando o assistente de instalação |Se você usar um SQL Server completo: DBO (ou semelhante) do banco de dados do mecanismo de sincronização |Faça alterações no nível do banco de dados, como atualizar tabelas com novas colunas. |

## <a name="more-about-the-created-accounts"></a>Mais informações sobre as contas criadas
### <a name="ad-ds-connector-account"></a>Conta do conector de AD DS
Se você usar configurações expressas, uma conta será criada em Active Directory que é usada para sincronização. A conta criada está localizada no domínio raiz da floresta no contêiner Usuários e tem seu nome prefixado com **MSOL_** . A conta é criada com uma senha longa e complexa que não expira. Se você tiver uma política de senha em seu domínio, verifique se as senhas longas e complexas seriam permitidas para essa conta.

![Conta do AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Se você usar configurações personalizadas, será responsável por criar a conta antes de iniciar a instalação.  Consulte criar a conta do conector de AD DS.

### <a name="adsync-service-account"></a>Conta de serviço do ADSync
O serviço de sincronização pode ser executado em contas diferentes. Ele pode ser executado em uma VSA ( **conta de serviço virtual** ), uma **conta de serviço gerenciado de grupo** (gMSA/sMSA) ou uma conta de usuário normal. As opções com suporte foram alteradas com a versão de 2017 de abril do Connect quando você realiza uma nova instalação. Se você atualizar de uma versão anterior do Azure AD Connect, essas opções adicionais não estarão disponíveis.

| Tipo de conta | Opção de instalação | Descrição |
| --- | --- | --- |
| [Conta de serviço virtual](#virtual-service-account) | Expresso e personalizado, 2017 de abril e posterior | Essa é a opção usada para todas as instalações expressas, exceto para instalações em um controlador de domínio. Para personalizado, é a opção padrão, a menos que outra opção seja usada. |
| [Conta de serviço gerenciado de grupo](#group-managed-service-account) | Personalizado, 2017 de abril e posterior | Se você usar um SQL Server remoto, será recomendável usar uma conta de serviço gerenciado de grupo. |
| [Conta de usuário](#user-account) | Expresso e personalizado, 2017 de abril e posterior | Uma conta de usuário prefixada com AAD_ é criada somente durante a instalação quando instalada no Windows Server 2008 e quando instalada em um controlador de domínio. |
| [Conta de usuário](#user-account) | Expressa e personalizada, 2017 de março e anterior | Uma conta local prefixada com AAD_ é criada durante a instalação. Ao usar a instalação personalizada, outra conta pode ser especificada. |

Se você usar o Connect com uma compilação de 2017 de março ou anterior, não deverá redefinir a senha na conta de serviço, uma vez que o Windows destrói as chaves de criptografia por motivos de segurança. Você não pode alterar a conta para nenhuma outra conta sem reinstalar Azure AD Connect. Se você atualizar para uma compilação de 2017 de abril ou posterior, haverá suporte para alterar a senha na conta de serviço, mas não será possível alterar a conta usada.

> [!Important]
> Você só pode definir a conta de serviço na primeira instalação. Não há suporte para alterar a conta de serviço após a conclusão da instalação.

Esta é uma tabela das opções padrão, recomendadas e com suporte para a conta de serviço de sincronização.

Legenda

- **Bold** indica a opção padrão e, na maioria dos casos, a opção recomendada.
- *Itálico* indica a opção recomendada quando não é a opção padrão.
- 2008-opção padrão quando instalada no Windows Server 2008
- Opção sem negrito-com suporte
- Conta local-conta de usuário local no servidor
- Conta de domínio-conta de usuário do domínio
- sMSA- [conta de serviço gerenciado autônomo](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA- [conta de serviço gerenciado de grupo](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Personalizado | SQL remoto</br>Personalizado |
| --- | --- | --- | --- |
| **computador autônomo/de grupo de trabalho** | Não suportado | **VSA**</br>Conta local (2008)</br>Conta local |  Não suportado |
| **computador ingressado no domínio** | **VSA**</br>Conta local (2008) | **VSA**</br>Conta local (2008)</br>Conta local</br>Conta de domínio</br>sMSA, gMSA | **gMSA**</br>Conta de domínio |
| **Controlador de domínio** | **Conta de domínio** | *gMSA*</br>**Conta de domínio**</br>sMSA| *gMSA*</br>**Conta de domínio**|

#### <a name="virtual-service-account"></a>Conta de serviço virtual
Uma conta de serviço virtual é um tipo especial de conta que não tem uma senha e é gerenciada pelo Windows.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

O VSA destina-se a ser usado com cenários em que o mecanismo de sincronização e o SQL estão no mesmo servidor. Se você usar o SQL remoto, será recomendável usar uma conta de serviço gerenciado de grupo em vez disso.

Esse recurso requer o Windows Server 2008 R2 ou posterior. Se você instalar o Azure AD Connect no Windows Server 2008, a instalação voltará a usar uma [conta de usuário](#user-account) em vez disso.

#### <a name="group-managed-service-account"></a>Conta de serviço gerenciado de grupo
Se você usar um SQL Server remoto, recomendamos o uso de uma **conta de serviço gerenciado de grupo**. Para obter mais informações sobre como preparar seu Active Directory para conta de serviço gerenciado de grupo, consulte [visão geral de contas de serviço gerenciado de grupo](https://technet.microsoft.com/library/hh831782.aspx).

Para usar essa opção, na página [instalar componentes necessários](how-to-connect-install-custom.md#install-required-components) , selecione **usar uma conta de serviço existente**e selecione **conta de serviço gerenciado**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Também há suporte para usar uma [conta de serviço gerenciado autônomo](https://technet.microsoft.com/library/dd548356.aspx). No entanto, eles só podem ser usados no computador local e não há nenhum benefício para usá-los na conta de serviço virtual padrão.

Esse recurso requer o Windows Server 2012 ou posterior. Se você precisar usar um sistema operacional mais antigo e usar o SQL remoto, deverá usar uma [conta de usuário](#user-account).

#### <a name="user-account"></a>Conta de usuário
Uma conta de serviço local é criada pelo assistente de instalação do (a menos que você especifique a conta a ser usada em configurações personalizadas). A conta é prefixada **AAD_** e usada para que o serviço de sincronização real seja executado como. Se você instalar Azure AD Connect em um controlador de domínio, a conta será criada no domínio. A conta de serviço **AAD_** deve estar localizada no domínio se:
   - Você usa um servidor remoto executando o SQL Server
   - Você usa um proxy que requer autenticação

![Conta de serviço de sincronização](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma senha longa e complexa que não expira.

Essa conta é usada para armazenar as senhas para as outras contas de forma segura. Essas outras senhas de contas são armazenadas criptografadas no banco de dados. As chaves privadas para as chaves de criptografia são protegidas com a criptografia de chave secreta dos serviços de criptografia usando a API de proteção de dados do Windows (DPAPI).

Se você usar uma SQL Server completa, a conta de serviço será o DBO do banco de dados criado para o mecanismo de sincronização. O serviço não funcionará conforme o esperado com nenhuma outra permissão. Um logon do SQL também é criado.

A conta também recebe permissões para arquivos, chaves do registro e outros objetos relacionados ao mecanismo de sincronização.

### <a name="azure-ad-connector-account"></a>Conta do conector do AD do Azure
Uma conta no Azure AD é criada para o uso do serviço de sincronização. Essa conta pode ser identificada por seu nome de exibição.

![Conta do AD](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

O nome do servidor em que a conta é usada pode ser identificado na segunda parte do nome de usuário. Na imagem, o nome do servidor é DC1. Se você tiver servidores de preparo, cada servidor terá sua própria conta.

A conta é criada com uma senha longa e complexa que não expira. Ele recebe uma função especial **contas de sincronização de diretório** que tem apenas permissões para executar tarefas de sincronização de diretório. Esta função interna especial não pode ser concedida fora do assistente de Azure AD Connect. O portal do Azure mostra essa conta com a função **usuário**.

Há um limite de 20 contas de serviço de sincronização no Azure AD. Para obter a lista de contas de serviço do Azure AD existentes no Azure AD, execute o seguinte cmdlet do PowerShell do Azure AD: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Para remover contas de serviço do Azure AD não usadas, execute o seguinte cmdlet do PowerShell do Azure AD: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Antes de usar os comandos do PowerShell acima, você precisará instalar o [módulo Azure Active Directory PowerShell for Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) e conectar-se à sua instância do AD do Azure usando [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Para obter informações adicionais sobre como gerenciar ou redefinir a senha para a conta do conector do Azure AD, consulte [gerenciar a conta de Azure ad Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Documentação relacionada
Se você não leu a documentação sobre como [integrar suas identidades locais com o Azure Active Directory](whatis-hybrid-identity.md), a tabela a seguir fornecerá links para tópicos relacionados.

|Tópico |Ligação|  
| --- | --- |
|Transferir o Azure AD Connect | [Transferir o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](how-to-connect-install-express.md)|
|Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)|
|Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verificar a instalação e atribuir licenças](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
