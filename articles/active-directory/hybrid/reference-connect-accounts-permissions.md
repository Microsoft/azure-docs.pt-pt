---
title: 'Azure AD Connect: Contas e permissões Microsoft Docs'
description: Este tópico descreve as contas utilizadas e criadas e as permissões necessárias.
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
ms.date: 01/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0771687aa7ccf9b749c107a6b1c507cb3ba08d
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881944"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: contas e permissões

## <a name="accounts-used-for-azure-ad-connect"></a>Contas utilizadas para Azure AD Connect

![visão geral de contas](media/reference-connect-accounts-permissions/account5.png)

O Azure AD Connect utiliza 3 contas para sincronizar informações de instalações ou do Windows Server Ative Directory para o Azure Ative Directory.  Estas contas são:

- **Conta de conector AD DS**: usada para ler/escrever informações para o Windows Server Ative Directory

- **Conta de serviço ADSync**: usado para executar o serviço de sincronização e aceder à base de dados SQL

- **Conta Azure AD Connector**: usado para escrever informações para Azure AD

Além destas três contas utilizadas para executar o Azure AD Connect, também necessitará das seguintes contas adicionais para instalar o Azure AD Connect.  Esses avisos são:

- **Conta administrador local**: O administrador que está a instalar o Azure AD Connect e que tem permissões de administrador local na máquina.

- **Conta administrador da empresa AD DS**: Opcionalmente utilizada para criar a "conta AD DS Connector" acima.

- **Conta Azure AD Global Administrator**: usada para criar a conta Azure AD Connector e configurar Azure AD.  Pode ver contas de administrador global no portal azul.  Ver [Ver Funções](../../active-directory/roles/manage-roles-portal.md#view-all-roles).

- **Conta SQL SA (opcional)**: usada para criar a base de dados ADSync ao utilizar a versão completa do SQL Server.  Este SQL Server pode ser local ou remoto para a instalação Azure AD Connect.  Esta conta pode ser a mesma que o Administrador da Empresa.  O fornecimento da base de dados pode agora ser realizado fora da banda pelo administrador SQL e depois instalado pelo administrador Azure AD Connect com direitos de proprietário de base de dados.  Para obter informações sobre isto consulte [instalar Azure AD Connect usando permissões de administrador delegadas sql](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> A partir da construção 1.4.###.# já não é suportado para usar uma administração de empresa ou uma conta de administração de domínio como conta AD DS Connector.  Se tentar introduzir uma conta que seja um administrador de empresa ou administrador de domínio ao especificar a **utilização da conta existente,** receberá um erro.

> [!NOTE]
> É apoiado para gerir as contas administrativas utilizadas no Azure AD Connect a partir de uma Floresta Administrativa ESAE (também conhecido como "Floresta Vermelha").
> As florestas administrativas dedicadas permitem que as organizações alojem contas administrativas, estações de trabalho e grupos num ambiente com controlos de segurança mais fortes do que o ambiente de produção.
> Para saber mais sobre florestas administrativas dedicadas, consulte a [ESAE Administrative Forest Design Approach](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach).

> [!NOTE]
> A função de Administrador Global não é necessária após a configuração inicial e a única conta necessária será a conta de **função de Conta sincronização do Diretório.** Isso não significa necessariamente que irá querer apenas remover a conta com o papel de Administrador Global. É melhor mudar o papel para um papel menos poderoso, já que remover totalmente a conta pode introduzir problemas se precisar de voltar a executar o assistente. Ao reduzir o privilégio do papel, pode sempre re-elevar os privilégios se tiver de utilizar novamente o assistente Azure AD Connect. 

## <a name="installing-azure-ad-connect"></a>Instalação Azure AD Connect
O assistente de instalação Azure AD Connect oferece dois caminhos diferentes:

* Nas Definições Expressas, o assistente requer mais privilégios.  Isto é para que possa configurar facilmente a sua configuração, sem que você crie utilizadores ou configuure permissões.
* Nas Definições Personalizadas, o assistente oferece-lhe mais opções e opções. No entanto, existem algumas situações em que precisa de garantir que tem as permissões corretas.



## <a name="express-settings-installation"></a>Instalação de configurações expressas
Nas definições express, o assistente de instalação solicita o seguinte:

  - Credenciais de administrador da empresa AD DS
  - Credenciais de administrador global da Azure AD

### <a name="ad-ds-enterprise-admin-credentials"></a>Credenciais de administração da AD DS Enterprise
A conta AD DS Enterprise Admin é usada para configurar o seu Ative Directory no local. Estas credenciais são utilizadas apenas durante a instalação e não são utilizadas após a conclusão da instalação. O Administrador da Empresa, não o Administrador de Domínio, deve certificar-se de que as permissões no Ative Directory podem ser definidas em todos os domínios.

Se estiver a atualizar a partir da DirSync, as credenciais AD DS Enterprise Admins são usadas para redefinir a palavra-passe para a conta utilizada pela DirSync. Também precisa de credenciais de Administrador Global da Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Credenciais de administração global da Azure AD
Estas credenciais são utilizadas apenas durante a instalação e não são utilizadas após a conclusão da instalação. É utilizado para criar a conta Azure AD Connector utilizada para sincronizar alterações no AD AZure. A conta também permite sincronização como uma funcionalidade no Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>A conta AD DS Connector exigiu permissões para configurações expressas
A conta AD DS Connector foi criada para ler e escrever para o Windows Server AD e tem as seguintes permissões quando criada por definições expressas:

| Permissão | Utilizado para |
| --- | --- |
| <li>Alterar o diretório de replicação</li><li>Replicar mudanças de diretório tudo |Sincronização de haxixe de palavra-passe |
| Ler/Escrever todas as propriedades Utilizador |Híbrido de importação e troca |
| Ler/Escrever todas as propriedades iNetOrgPerson |Híbrido de importação e troca |
| Ler/Escrever todas as propriedades Grupo |Híbrido de importação e troca |
| Ler/Escrever todas as propriedades Contato |Híbrido de importação e troca |
| Repor palavra-passe |Preparação para permitir a inscrição de passwords |

### <a name="express-installation-wizard-summary"></a>Resumo do assistente de instalação expresso

![Instalação expressa](./media/reference-connect-accounts-permissions/express.png)

Segue-se um resumo das páginas de assistentes de instalação expressa, as credenciais recolhidas e para que são utilizadas.

| Página do assistente | Credenciais Recolhidas | Permissões Necessárias | Usado para |
| --- | --- | --- | --- |
| N/D |Utilizador executando o assistente de instalação |Administrador do servidor local |<li>Cria a conta de serviço ADSync que é usada para executar o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório AD Azure |Papel de administrador global no AD Azure |<li>Ativando a sincronização no diretório AD Azure.</li>  <li>Criação da conta Azure AD Connector que é usada para operações de sincronização em curso em Azure AD.</li> |
| Ligar ao AD DS |Credenciais de Diretório Ativo no local |Membro do grupo Enterprise Admins (EA) em Ative Directory |<li>Cria a conta AD DS Connector no Ative Directory e concede-lhe permissões. Esta conta criada é usada para ler e escrever informações de diretórios durante a sincronização.</li> |


## <a name="custom-installation-settings"></a>Configurações de instalação personalizadas

Com a instalação de definições personalizadas, o assistente oferece-lhe mais opções e opções. 

### <a name="custom-installation-wizard-summary"></a>Resumo do assistente de instalação personalizado

Segue-se um resumo das páginas de assistentes de instalação personalizadas, das credenciais recolhidas e para que são utilizadas.

![Screenshot que mostra as páginas de assistente de instalação personalizadas.](./media/reference-connect-accounts-permissions/customize.png)

| Página do assistente | Credenciais Recolhidas | Permissões Necessárias | Usado para |
| --- | --- | --- | --- |
| N/D |Utilizador executando o assistente de instalação |<li>Administrador do servidor local</li><li>Se utilizar um Servidor SQL completo, o utilizador deve ser administrador de sistema (SA) em SQL</li> |Por predefinição, cria a conta local que é usada como conta de serviço do motor de sincronização. A conta só é criada quando o administrador não especifica uma determinada conta. |
| Instalar serviços de sincronização, opção de conta de serviço |Credenciais de conta de utilizador ad ou local |Utilizador, as permissões são concedidas pelo assistente de instalação |Se o administrador especificar uma conta, esta conta é utilizada como conta de serviço para o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório AD Azure |Papel de administrador global no AD Azure |<li>Ativando a sincronização no diretório AD Azure.</li>  <li>Criação da conta Azure AD Connector que é usada para operações de sincronização em curso em Azure AD.</li> |
| Ligar os diretórios |Credenciais de Diretório Ativo no local para cada floresta que esteja ligada ao Azure AD |As permissões dependem das funcionalidades que ativa e pode ser encontrada na conta Criar o Conector DS AD |Esta conta é usada para ler e escrever informações de diretório durante a sincronização. |
| Servidores do AD FS |Para cada servidor da lista, o assistente recolhe credenciais quando as credenciais de inscrição do utilizador que executa o assistente são insuficientes para se ligar |Administrador de Domínio |Instalação e configuração da função do servidor AD FS. |
| Servidores de procuração de aplicações web |Para cada servidor da lista, o assistente recolhe credenciais quando as credenciais de inscrição do utilizador que executa o assistente são insuficientes para se ligar |Administração local na máquina alvo |Instalação e configuração da função do servidor WAP. |
| Credenciais de confiança proxy |Credenciais de confiança de serviço da Federação (as credenciais que o representante usa para se inscrever para um certificado fiduciário do FS |Conta de domínio que é um administrador local do servidor AD FS |Inscrição inicial do certificado fiduciário FS-WAP. |
| Página de Conta de Serviço AD FS, "Use uma opção de conta de utilizador de domínio" |Credenciais de conta de utilizador de AD |Utilizador de domínio |A conta de utilizador Azure AD cujas credenciais são fornecidas é utilizada como a conta de inscrição do serviço AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Criar a conta AD DS Connector

>[!IMPORTANT]
>Um novo Módulo PowerShell chamado ADSyncConfig.psm1 foi introduzido com a construção **1.1.880.0** (lançado em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões corretas do Ative Directory para a conta Azure AD DS Connector.
>
>Para mais informações consulte [Azure AD Connect: Confmissão](how-to-connect-configure-ad-ds-connector-account.md) de conta de conector AD DS

A conta que especifica na página 'Ligar os **directórios'** deve estar presente no Ative Directory antes da instalação.  Azure AD Connect versão 1.1.524.0 e mais tarde tem a opção de permitir que o assistente Azure AD Connect cria a **conta AD DS Connector** usada para ligar ao Ative Directory.  

Deve também ter as permissões necessárias concedidas. O assistente de instalação não verifica as permissões e quaisquer problemas só são encontrados durante a sincronização.

Quais as permissões que necessita dependem das funcionalidades opcionais que ativa. Se tiver vários domínios, as permissões devem ser concedidas para todos os domínios da floresta. Se não ativar nenhuma destas funcionalidades, as permissões do **Utilizador do Domínio** predefinido são suficientes.

| Funcionalidade | Permissões |
| --- | --- |
| recurso ms-DS-ConsistênciaGuid |Escreva permissões para o atributo ms-DS-ConsistênciaGuid documentado em [Conceitos de Design - Usando ms-DS-ConsistênciaGuid como fonteAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de haxixe de palavra-passe |<li>Alterar o diretório de replicação</li>  <li>Replicar mudanças de diretório tudo |
| Implementação híbrida de troca |Escreva permissões para os atributos documentados na [writeback híbrida Exchange](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para utilizadores, grupos e contactos. |
| Troca de Correio Público Pasta |Leia as permissões dos atributos documentados na [Pasta Pública do Correio da Manhã](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Repetição de escrita de palavras-passe |Escreva permissões para os atributos documentados em [Começar com a gestão de passwords](../authentication/tutorial-enable-sspr-writeback.md) para os utilizadores. |
| Repetição de escrita do dispositivo |Permissões concedidas com um script PowerShell como descrito na [gravação do dispositivo](how-to-connect-device-writeback.md). |
| Repetição de escrita do grupo |Permite-lhe escrever os **Grupos Microsoft 365** para uma floresta com o Exchange instalado.|

## <a name="upgrade"></a>Atualizar
Quando atualiza de uma versão do Azure AD Connect para uma nova versão, precisa das seguintes permissões:

>[!IMPORTANT]
>Começando pela construção 1.1.484, o Azure AD Connect introduziu um bug de regressão que requer permissões sysadmin para atualizar a base de dados SQL.  Este bug é corrigido na construção 1.1.647.  Se estiver a atualizar para esta construção, precisará de permissões sysadmin.  Permissões dbo não são suficientes.  Se tentar atualizar o Azure AD Connect sem ter permissões sysadmin, a atualização falhará e o Azure AD Connect deixará de funcionar corretamente depois.  A Microsoft está ciente disso e está a trabalhar para corrigir isto.


| Principal | Permissões necessárias | Utilizado para |
| --- | --- | --- |
| Utilizador executando o assistente de instalação |Administrador do servidor local |Atualizar binários. |
| Utilizador executando o assistente de instalação |Membro da ADSyncAdmins |Faça alterações nas Regras de Sincronização e noutras configurações. |
| Utilizador executando o assistente de instalação |Se utilizar um servidor SQL completo: DBO (ou similar) da base de dados do motor de sincronização |Faça alterações no nível da base de dados, tais como atualizar tabelas com novas colunas. |

## <a name="more-about-the-created-accounts"></a>Mais sobre as contas criadas
### <a name="ad-ds-connector-account"></a>Conta do Conector do AD DS
Se utilizar definições expressas, é criada uma conta no Ative Directory que é usada para sincronização. A conta criada está localizada no domínio das raízes da floresta no contentor Utilizadores e tem o seu nome pré-fixado com **MSOL_**. A conta é criada com uma senha complexa e longa que não expira. Se tiver uma política de senha no seu domínio, certifique-se de que senhas longas e complexas serão permitidas para esta conta.

![Conta AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Se utilizar definições personalizadas, é responsável por criar a conta antes de iniciar a instalação.  Consulte Criar a conta AD DS Connector.

### <a name="adsync-service-account"></a>ADSync service account (conta de serviço do ADSync)
O serviço de sincronização pode ser executado em diferentes contas. Pode ser executado numa **Conta de Serviço Virtual** (VSA), numa Conta de Serviço Gerido pelo **Grupo** (gMSA/sMSA), ou numa conta de utilizador regular. As opções suportadas foram alteradas com o lançamento de abril de 2017 do Connect quando se faz uma nova instalação. Se atualizar a partir de uma versão anterior do Azure AD Connect, estas opções adicionais não estão disponíveis.

| Tipo de conta | Opção de instalação | Description |
| --- | --- | --- |
| [Conta de Serviço Virtual](#virtual-service-account) | Expresso e personalizado, abril de 2017 e mais tarde | Esta é a opção utilizada para todas as instalações expressas, com exceção das instalações num Controlador de Domínio. Para o costume, é a opção padrão a menos que outra opção seja usada. |
| [Conta de Serviço Gerida de Grupo](#group-managed-service-account) | Personalizado, abril de 2017 e mais tarde | Se utilizar um servidor SQL remoto, recomendamos a utilização de uma conta de serviço gerida pelo grupo. |
| [Conta de utilizador](#user-account) | Expresso e personalizado, abril de 2017 e mais tarde | Uma conta de utilizador pré-fixa com AAD_ só é criada durante a instalação quando instalada no Windows Server 2008 e quando instalada num Controlador de Domínio. |
| [Conta de utilizador](#user-account) | Expresso e personalizado, março de 2017 e mais cedo | Uma conta local pré-fixa com AAD_ é criada durante a instalação. Ao utilizar a instalação personalizada, pode especificar outra conta. |

Se utilizar o Connect com uma construção a partir de março de 2017 ou mais cedo, então não deverá redefinir a palavra-passe na conta de serviço, uma vez que o Windows destrói as chaves de encriptação por razões de segurança. Não é possível alterar a conta para qualquer outra conta sem reinstalar o Azure AD Connect. Se fizer o upgrade para uma construção a partir de abril de 2017 ou posterior, então é suportado para alterar a palavra-passe na conta de serviço, mas não pode alterar a conta utilizada.

> [!Important]
> Só pode definir a conta de serviço na primeira instalação. Não é suportado para alterar a conta de serviço depois de concluída a instalação.

Esta é uma tabela das opções padrão, recomendadas e suportadas para a conta de serviço de sincronização.

Legenda:

- **O negrito** indica a opção predefinição e, na maioria dos casos, a opção recomendada.
- *Itálico* indica a opção recomendada quando não é a opção predefinitiva.
- 2008 - Opção padrão quando instalado no Windows Server 2008
- Não-arrojado - Opção suportada
- Conta local - Conta de utilizador local no servidor
- Conta de domínio - Conta de utilizador de domínio
- sMSA - [conta de Serviço Gerido Autónomo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA - [conta de serviço gerido do grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Personalizado | SQL remoto</br>Personalizado |
| --- | --- | --- | --- |
| **máquina unida ao domínio** | **VSA**</br>Conta local (2008) | **VSA**</br>Conta local (2008)</br>Conta local</br>Conta do domínio</br>sMSA,gMSA | **gMSA**</br>Conta do domínio |
| **Controlador de domínio** | **Conta do domínio** | *gMSA*</br>**Conta do domínio**</br>sMSA| *gMSA*</br>**Conta do domínio**|

#### <a name="virtual-service-account"></a>Conta de serviço virtual
Uma conta de serviço virtual é um tipo especial de conta que não tem senha e é gerida pelo Windows.

![Screenshot que mostra a conta de serviço virtual (VSA).](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

O VSA destina-se a ser utilizado com cenários em que o motor de sincronização e o SQL se encontram no mesmo servidor. Se utilizar o SQL remoto, recomendamos a utilização de uma conta de serviço gerida pelo grupo.

Esta funcionalidade requer o Windows Server 2008 R2 ou mais tarde. Se instalar o Azure AD Connect no Windows Server 2008, a instalação volta a utilizar uma [conta de utilizador.](#user-account)

#### <a name="group-managed-service-account"></a>Conta de serviço gerida pelo grupo
Se utilizar um servidor SQL remoto, recomendamos a utilização de uma **conta de serviço gerida pelo grupo.** Para obter mais informações sobre como preparar o seu Diretório Ativo para a conta de Serviço Gerido pelo Grupo, consulte a Visão Geral das [Contas de Serviço Geridas pelo Grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)).

Para utilizar esta opção, na página [de componentes necessários,](how-to-connect-install-custom.md#install-required-components) selecione **Utilize uma conta de serviço existente** e selecione Conta de Serviço **Gerido**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Também é suportado para utilizar uma conta de [serviço gerida autónoma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10)). No entanto, estes apenas podem ser utilizados na máquina local e não há qualquer benefício em usá-los sobre a conta de serviço virtual padrão.

Esta funcionalidade requer o Windows Server 2012 ou mais tarde. Se precisar de utilizar um sistema operativo mais antigo e utilizar o SQL remoto, então tem de utilizar uma [conta de utilizador](#user-account).

#### <a name="user-account"></a>Conta de utilizador
Uma conta de serviço local é criada pelo assistente de instalação (a menos que especifique a conta a utilizar em configurações personalizadas). A conta é pré-fixa **AAD_** e usada para o serviço de sincronização real funcionar como. Se instalar o Azure AD Connect num Controlador de Domínio, a conta é criada no domínio. A **conta de** serviço AAD_ deve estar localizada no domínio se:
   - você usa um servidor remoto executando servidor SQL
   - você usa um proxy que requer autenticação

![Conta de Serviço sincronizado](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma senha complexa e longa que não expira.

Esta conta é utilizada para armazenar as palavras-passe das outras contas de forma segura. Estas outras palavras-passe de contas são armazenadas encriptadas na base de dados. As chaves privadas das chaves de encriptação estão protegidas com a encriptação de chaves secretas dos serviços criptográficos utilizando a API de Proteção de Dados do Windows (DPAPI).

Se utilizar um SqL Server completo, então a conta de serviço é o DBO da base de dados criada para o motor de sincronização. O serviço não funcionará como pretendido com quaisquer outras permissões. Também é criado um login SQL.

A conta também é concedida permissões a ficheiros, chaves de registo e outros objetos relacionados com o Motor de Sincronização.

### <a name="azure-ad-connector-account"></a>Conta do Conector do Azure AD
Uma conta em Azure AD é criada para o uso do serviço de sincronização. Esta conta pode ser identificada pelo seu nome de exibição.

![Screenshot que mostra a conta AZure AD.](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

O nome do servidor em que a conta é utilizada pode ser identificado na segunda parte do nome de utilizador. Na imagem, o nome do servidor é DC1. Se tiver servidores de paragem, cada servidor tem a sua própria conta.

A conta é criada com uma senha complexa e longa que não expira. É-lhe concedida uma função especial Contas de Sincronização de **Diretórios** que tem apenas permissões para executar tarefas de sincronização de diretórios. Esta função especial incorporada não pode ser concedida fora do assistente Azure AD Connect. O portal Azure mostra esta conta com a função **Utilizador**.

Existe um limite de 20 contas de serviço sincronizado no Azure AD. Para obter a lista das contas de serviço AZure AD existentes no seu AD Azure, execute o seguinte cmdlet Azure AD PowerShell: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Para remover as contas de serviço Azure AD não reutilizadas, execute o seguinte cmdlet Azure AD PowerShell: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Antes de poder utilizar os comandos PowerShell acima, terá de instalar o [PowerShell do Diretório Ativo Azure para módulo Graph](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module) e ligar-se à sua instância de Azure AD utilizando [o Connect-AzureAD](/powershell/module/azuread/connect-azuread)

Para obter informações adicionais sobre como gerir ou redefinir a palavra-passe da conta Azure AD Connector ver [Gerir a conta Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Documentação relacionada
Se não leu a documentação sobre [a integração das suas identidades no local com o Azure Ative Directory,](whatis-hybrid-identity.md)a tabela seguinte fornece ligações a tópicos relacionados.

|Tópico |Ligação|  
| --- | --- |
|Transferir o Azure AD Connect | [Transferir o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](how-to-connect-install-express.md)|
|Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)|
|Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verificar a instalação e atribuir licenças](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
