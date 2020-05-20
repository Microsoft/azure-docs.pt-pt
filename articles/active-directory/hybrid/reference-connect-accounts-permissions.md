---
title: 'Azure AD Connect: Contas e permissões / Microsoft Docs'
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
ms.date: 05/18/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23508535c1853cd056bb162c254cda5f7f86d7fe
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681744"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: contas e permissões

## <a name="accounts-used-for-azure-ad-connect"></a>Contas utilizadas para o Azure AD Connect

![resumo das contas](media/reference-connect-accounts-permissions/account5.png)

O Azure AD Connect utiliza 3 contas para sincronizar informações do Diretório Ativo do Servidor do Windows para o Diretório Ativo do Azure.  Estas contas são:

- **Conta DeConector AD DS**: utilizada para ler/escrever informações para o Diretório Ativo do Servidor do Windows

- **Conta de serviço ADSync**: utilizada para executar o serviço de sincronização e aceder à base de dados SQL

- **Conta de Conector Azure AD**: usada para escrever informações para a Azure AD

Além destas três contas utilizadas para executar o Azure AD Connect, também necessitará das seguintes contas adicionais para instalar o Azure AD Connect.  Nomeadamente:

- **Conta de Administrador Local**: O administrador que está a instalar o Azure AD Connect e que tem permissões de administrador local na máquina.

- **Conta de Administrador de Empresa AD DS**: Opcionalmente utilizada para criar a "conta de Conector AD DS" acima.

- **Conta De Administrador Global Azure AD**: usada para criar a conta de Conector Azure AD e configurar a AD Azure.

- **Conta SQL SA (opcional)**: utilizada para criar a base de dados ADSync ao utilizar a versão completa do Servidor SQL.  Este Servidor SQL pode ser local ou remoto para a instalação Azure AD Connect.  Esta conta pode ser a mesma conta que o Administrador da Empresa.  O fornecimento da base de dados pode agora ser realizado fora de banda pelo administrador SQL e, em seguida, instalado pelo administrador Azure AD Connect com direitos de proprietário de base de dados.  Para obter informações sobre este ponto consulte [Instalar o Azure AD Connect utilizando permissões de administrador delegados SQL](how-to-connect-install-sql-delegation.md)


>[!IMPORTANT]
> A partir da construção 1.4.###########################não é mais suportado para usar uma administração da empresa ou uma conta de administração de domínio como a conta AD DS Connector.  Se tentar introduzir uma conta que seja uma administração ou administrador de domínio da empresa ao especificar a **utilização da conta existente,** receberá um erro.

> [!NOTE]
> É suportado para gerir as contas administrativas utilizadas no Azure AD Connect a partir de uma Floresta Administrativa da ESAE (também conhecido como "Floresta Vermelha").
> As florestas administrativas dedicadas permitem que as organizações alojem contas administrativas, estações de trabalho e grupos num ambiente com controlos de segurança mais fortes do que o ambiente de produção.
> Para saber mais sobre florestas administrativas dedicadas, consulte a Abordagem de [Design Florestal Administrativo da ESAE.](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach)

> [!NOTE]
> A função de Administrador Global não é necessária após a configuração inicial e a única conta necessária será a conta de sincronização do **Diretório.** Isso não significa necessariamente que queira apenas remover a conta com o papel de Administrador Global. É melhor mudar o papel para um papel menos poderoso, já que remover totalmente a conta pode introduzir problemas se precisar de reexecutar o assistente novamente. Reduzindo o privilégio do papel, pode sempre reelevar os privilégios se tiver de voltar a utilizar o assistente Azure AD Connect. 

## <a name="installing-azure-ad-connect"></a>Instalação de Ligação AD Azure
O assistente de instalação Azure AD Connect oferece dois caminhos diferentes:

* Em Definições Expressas, o assistente requer mais privilégios.  Isto é para que possa configurar facilmente a sua configuração, sem exigir que crie utilizadores ou configure permissões.
* Em Definições Personalizadas, o assistente oferece-lhe mais opções e opções. No entanto, existem algumas situações em que precisa de garantir que tem as permissões corretas.



## <a name="express-settings-installation"></a>Instalação de configurações expressas
Nas definições expressas, o assistente de instalação pede o seguinte:

  - Credenciais de administrador de empresa AD DS
  - Credenciais de Administrador Global da AD Azure

### <a name="ad-ds-enterprise-admin-credentials"></a>Credenciais de administração da AD DS Enterprise Admin
A conta AD DS Enterprise Admin é usada para configurar o seu Diretório Ativo no local. Estas credenciais só são utilizadas durante a instalação e não são utilizadas após a instalação ter sido concluída. O Administrador da Empresa, não o Administrador de Domínio deve certificar-se de que as permissões em Diretório Ativo podem ser definidas em todos os domínios.

Se estiver a atualizar a partir do DirSync, as credenciais ad DS Enterprise Admins são usadas para redefinir a palavra-passe para a conta utilizada pelo DirSync. Também precisa de credenciais de Administrador Global Da AD Azure.

### <a name="azure-ad-global-admin-credentials"></a>Credenciais de administração global da Azure AD
Estas credenciais só são utilizadas durante a instalação e não são utilizadas após a instalação ter sido concluída. É utilizado para criar a conta Azure AD Connector utilizada para sincronizar alterações no Azure AD. A conta também permite sincronizar como recurso em Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Conta de Conector AD DS exigia permissões para configurações expressas
A conta AD DS Connector é criada para leitura e escrita para a AD do Windows Server e tem as seguintes permissões quando criada por definições expressas:

| Permissão | Utilizado para |
| --- | --- |
| <li>Replicar alterações de diretório</li><li>Replicar mudanças de diretório todos |Sincronização de hash de palavra-passe |
| Ler/Escrever todas as propriedades Utilizador |Importação e Troca de Híbridos |
| Ler/Escrever todas as propriedades iNetOrgPerson |Importação e Troca de Híbridos |
| Ler/Escrever todas as propriedades Grupo |Importação e Troca de Híbridos |
| Ler/Escrever todas as propriedades Contacte |Importação e Troca de Híbridos |
| Repor palavra-passe |Preparação para permitir a reescrita da palavra-passe |

### <a name="express-installation-wizard-summary"></a>Resumo do assistente de instalação expressa

![Instalação expressa](./media/reference-connect-accounts-permissions/express.png)

Segue-se um resumo das páginas de assistentes de instalação expressa, das credenciais recolhidas e do que são utilizadas.

| Página do Feiticeiro | Credenciais recolhidas | Permissões necessárias | Usado para |
| --- | --- | --- | --- |
| N/D |Utilizador executando o assistente de instalação |Administrador do servidor local |<li>Cria a conta de serviço ADSync que é usada para executar o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório azure AD |Papel de administrador global na AD Azure |<li>Habilitar a sincronização no diretório Azure AD.</li>  <li>Criação da conta Azure AD Connector que é usada para operações de sincronização em curso em Azure AD.</li> |
| Ligar ao AD DS |Credenciais de Diretório Ativo no local |Membro do grupo Deadministradores empresariais (EA) em Diretório Ativo |<li>Cria a conta AD DS Connector em Diretório Ativo e concede-lhe permissões. Esta conta criada é usada para ler e escrever informações de diretório durante a sincronização.</li> |


## <a name="custom-installation-settings"></a>Configurações de instalação personalizadas

Com a instalação de configurações personalizadas, o assistente oferece-lhe mais opções e opções. 

### <a name="custom-installation-wizard-summary"></a>Resumo personalizado do assistente de instalação

Segue-se um resumo das páginas de assistentes de instalação personalizadas, das credenciais recolhidas e do que são usadas.

![Instalação expressa](./media/reference-connect-accounts-permissions/customize.png)

| Página do Feiticeiro | Credenciais recolhidas | Permissões necessárias | Usado para |
| --- | --- | --- | --- |
| N/D |Utilizador executando o assistente de instalação |<li>Administrador do servidor local</li><li>Se utilizar um Servidor SQL completo, o utilizador deve ser administrador de sistema (SA) no SQL</li> |Por padrão, cria a conta local que é usada como conta de serviço do motor sincronizado. A conta só é criada quando o administrador não especifica uma determinada conta. |
| Instalar serviços de sincronização, opção de conta de serviço |Credenciais de conta de utilizador ad ou local |Utilizador, as permissões são concedidas pelo assistente de instalação |Se o administrador especificar uma conta, esta conta é utilizada como conta de serviço para o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório azure AD |Papel de administrador global na AD Azure |<li>Habilitar a sincronização no diretório Azure AD.</li>  <li>Criação da conta Azure AD Connector que é usada para operações de sincronização em curso em Azure AD.</li> |
| Ligar os diretórios |No local credenciais de Diretório Ativo para cada floresta que está ligada ao Azure AD |As permissões dependem das funcionalidades que ativa e pode ser encontrada na conta Create the AD DS Connector |Esta conta é usada para ler e escrever informações de diretório durante a sincronização. |
| Servidores do AD FS |Para cada servidor da lista, o assistente recolhe credenciais quando as credenciais de inserção do utilizador que executa o assistente são insuficientes para ligar |Administrador de Domínio |Instalação e configuração da função do servidor AD FS. |
| Servidores proxy de aplicação web |Para cada servidor da lista, o assistente recolhe credenciais quando as credenciais de inserção do utilizador que executa o assistente são insuficientes para ligar |Administração local na máquina alvo |Instalação e configuração da função do servidor WAP. |
| Credenciais de confiança proxy |Credenciais de confiança de serviço da Federação (as credenciais que o representante usa para se inscrever para um certificado fiduciário da FS |Conta de domínio que é um administrador local do servidor AD FS |Inscrição inicial do certificado fidedigno FS-WAP. |
| Página da Conta de Serviço AD FS, "Use uma opção de conta de utilizador de domínio" |Credenciais de conta de utilizador aD |Utilizador de domínio |A conta de utilizador da AD Azure cujas credenciais são fornecidas é utilizada como conta de entrada do serviço AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Criar a conta de Conector AD DS

>[!IMPORTANT]
>Um novo Módulo PowerShell chamado ADSyncConfig.psm1 foi introduzido com a build **1.1.880.0** (lançado em agosto de 2018) que inclui uma coleção de cmdlets para ajudá-lo a configurar as permissões corretas do Diretório Ativo para a conta de Conector AD DS Azure.
>
>Para mais informações consulte [Azure AD Connect: Configure AD DS Conector Account Permission](how-to-connect-configure-ad-ds-connector-account.md)

A conta que especifica na página **Connect your Diretórios** deve estar presente no Diretório Ativo antes da instalação.  A versão 1.1.524.0 do Azure AD Connect tem a opção de permitir que o assistente Azure AD Connect crie a **conta de Conector AD DS** utilizada para se ligar ao Diretório Ativo.  

Deve também ter as permissões necessárias concedidas. O assistente de instalação não verifica as permissões e quaisquer problemas só são encontrados durante a sincronização.

Quais as permissões que necessita depende das funcionalidades opcionais que ativa. Se tiver vários domínios, as permissões devem ser concedidas para todos os domínios da floresta. Se não ativar nenhuma destas funcionalidades, as permissões padrão **do Utilizador** do Domínio são suficientes.

| Funcionalidade | Permissões |
| --- | --- |
| funcionalidade ms-DS-Consistência-Consistência |Escreva permissões ao atributo ms-DS-ConsistênciaGuid documentado em Conceitos de [Design - Utilizando ms-DS-ConsistênciaGuid como fonteAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de palavra-passe |<li>Replicar alterações de diretório</li>  <li>Replicar mudanças de diretório todos |
| Troca de implantação híbrida |Escreva permissões aos atributos documentados no [Exchange hybrid writeback](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para utilizadores, grupos e contactos. |
| Pasta Pública de Troca de Correio |Leia as permissões aos atributos documentados na Pasta Pública de [Troca de Correios](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para pastas públicas. | 
| Repetição de escrita de palavras-passe |Escreva permissões aos atributos documentados em [Iniciar-se com a gestão](../authentication/howto-sspr-writeback.md) de passwords para os utilizadores. |
| Repetição de escrita do dispositivo |Permissões concedidas com um script PowerShell conforme descrito na [reescrita](how-to-connect-device-writeback.md)do dispositivo . |
| Repetição de escrita do grupo |Permite-lhe reescrever o **Office 365 Groups** para uma floresta com exchange instalado.|

## <a name="upgrade"></a>Atualização
Quando atualizas de uma versão do Azure AD Connect para um novo lançamento, precisas das seguintes permissões:

>[!IMPORTANT]
>Começando com a construção 1.1.484, o Azure AD Connect introduziu um bug de regressão que requer permissões de sisadmina para atualizar a base de dados SQL.  Este bug é corrigido na construção 1.1.647.  Se estiver a atualizar para esta construção, precisará de permissões de sysadmin.  As permissões dbo não são suficientes.  Se tentar atualizar o Azure AD Connect sem ter permissões de sisadmina, a atualização falhará e o Azure AD Connect deixará de funcionar corretamente depois.  A Microsoft está ciente disso e está a trabalhar para corrigir isto.


| Diretor | Permissões necessárias | Utilizado para |
| --- | --- | --- |
| Utilizador executando o assistente de instalação |Administrador do servidor local |Atualizar binários. |
| Utilizador executando o assistente de instalação |Membro da ADSyncAdmins |Faça alterações nas Regras de Sincronização e outraconfiguração. |
| Utilizador executando o assistente de instalação |Se utilizar um servidor SQL completo: DBO (ou similar) da base de dados do motor sincronizado |Faça alterações no nível da base de dados, tais como atualizar tabelas com novas colunas. |

## <a name="more-about-the-created-accounts"></a>Mais sobre as contas criadas
### <a name="ad-ds-connector-account"></a>Conta do Conector do AD DS
Se utilizar configurações expressas, então é criada uma conta no Diretório Ativo que é usada para sincronização. A conta criada está localizada no domínio da raiz florestal no contentor utilizadores e tem o seu nome pré-fixado com **MSOL_**. A conta é criada com uma senha complexa e longa que não expira. Se tiver uma política de senha no seu domínio, certifique-se de que senhas longas e complexas serão permitidas para esta conta.

![Conta AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Se utilizar configurações personalizadas, é responsável por criar a conta antes de iniciar a instalação.  Consulte Criar a conta de Conector AD DS.

### <a name="adsync-service-account"></a>ADSync service account (conta de serviço do ADSync)
O serviço de sincronização pode ser executado sob contas diferentes. Pode ser executado sob uma Conta de **Serviço Virtual** (VSA), uma Conta de Serviço Gerida **pelo Grupo** (gMSA/sMSA) ou uma conta de utilizador regular. As opções suportadas foram alteradas com o lançamento de abril de 2017 do Connect quando fizer uma nova instalação. Se atualizar a partir de um lançamento anterior do Azure AD Connect, estas opções adicionais não estão disponíveis.

| Tipo de conta | Opção de instalação | Descrição |
| --- | --- | --- |
| [Conta de Serviço Virtual](#virtual-service-account) | Expresso e personalizado, abril de 2017 e mais tarde | Esta é a opção utilizada para todas as instalações expressas, com exceção das instalações de um controlador de domínio. Para personalizado, é a opção padrão a menos que outra opção seja usada. |
| [Conta de Serviço Gerida de Grupo](#group-managed-service-account) | Personalizado, abril de 2017 e mais tarde | Se utilizar um servidor SQL remoto, recomendamos que utilize uma conta de serviço gerida pelo grupo. |
| [Conta de utilizador](#user-account) | Expresso e personalizado, abril de 2017 e mais tarde | Uma conta de utilizador pré-fixada com AAD_ só é criada durante a instalação quando instalada no Windows Server 2008 e quando instalada num Controlador de Domínio. |
| [Conta de utilizador](#user-account) | Expresso e personalizado, março de 2017 e mais cedo | Uma conta local pré-fixada com AAD_ é criada durante a instalação. Ao utilizar uma instalação personalizada, outra conta pode ser especificada. |

Se utilizar o Connect com uma construção a partir de março de 2017 ou mais cedo, então não deverá redefinir a palavra-passe na conta de serviço, uma vez que o Windows destrói as chaves de encriptação por razões de segurança. Não é possível alterar a conta para qualquer outra conta sem reinstalar o Azure AD Connect. Se fizer o upgrade para uma construção a partir de abril de 2017 ou mais tarde, então é suportado para alterar a palavra-passe na conta de serviço, mas não pode alterar a conta utilizada.

> [!Important]
> Só é possível definir a conta de serviço na primeira instalação. Não é suportado para alterar a conta de serviço após a instalação ter sido concluída.

Esta é uma tabela das opções padrão, recomendadas e suportadas para a conta de serviço sincronizado.

Legenda:

- **O negrito** indica a opção por defeito e, na maioria dos casos, a opção recomendada.
- *Itálico* indica a opção recomendada quando não é a opção por defeito.
- 2008 - Opção predefinida quando instalada no Windows Server 2008
- Opção não ousada - Opção suportada
- Conta local - Conta de utilizador local no servidor
- Conta de domínio - Conta de utilizador de domínio
- sMSA - [conta de serviço gerido autónomo](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - conta de [serviço gerido](https://technet.microsoft.com/library/hh831782.aspx) pelo grupo

| | LocalDB</br>Express | LocalDB/LocalsQL</br>Personalizar | SQL remoto</br>Personalizar |
| --- | --- | --- | --- |
| **máquina autónoma/grupo de trabalho** | Não suportado | **VSA**</br>Conta local (2008)</br>Conta local |  Não suportado |
| **máquina unida ao domínio** | **VSA**</br>Conta local (2008) | **VSA**</br>Conta local (2008)</br>Conta local</br>Conta do domínio</br>sMSA,gMSA | **gMSA**</br>Conta do domínio |
| **Controlador de Domínio** | **Conta do domínio** | *gMSA*</br>**Conta do domínio**</br>sMSA| *gMSA*</br>**Conta do domínio**|

#### <a name="virtual-service-account"></a>Conta de serviço virtual
Uma conta de serviço virtual é um tipo especial de conta que não tem senha e é gerida pelo Windows.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

O VSA destina-se a ser utilizado com cenários em que o motor de sincronização e o SQL estão no mesmo servidor. Se utilizar o SQL remoto, recomendamos que utilize uma Conta de Serviço Gerida pelo Grupo.

Esta funcionalidade requer o Windows Server 2008 R2 ou mais tarde. Se instalar o Azure AD Connect no Windows Server 2008, a instalação volta a utilizar uma conta de [utilizador.](#user-account)

#### <a name="group-managed-service-account"></a>Conta de serviço gerida pelo grupo
Se utilizar um servidor SQL remoto, recomendamos a utilização de uma conta de **serviço gerida**por grupo . Para obter mais informações sobre como preparar o seu Diretório Ativo para a conta de Serviço Gerido pelo Grupo, consulte a visão geral das [Contas de Serviço geridas](https://technet.microsoft.com/library/hh831782.aspx)pelo grupo .

Para utilizar esta opção, na página de [componentes necessários,](how-to-connect-install-custom.md#install-required-components) selecione **Utilize uma conta de serviço existente**, e selecione Conta de Serviço **Gerida**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Também é suportado a utilização de uma conta de [serviço gerida autónoma.](https://technet.microsoft.com/library/dd548356.aspx) No entanto, estas só podem ser utilizadas na máquina local e não há qualquer benefício em utilizá-las sobre a conta de serviço virtual predefinida.

Esta funcionalidade requer o Windows Server 2012 ou mais tarde. Se necessitar de utilizar um sistema operativo mais antigo e utilizar O SQL remoto, então deve utilizar uma conta de [utilizador](#user-account).

#### <a name="user-account"></a>Conta de utilizador
Uma conta de serviço local é criada pelo assistente de instalação (a menos que especifique a conta a utilizar em configurações personalizadas). A conta é pré-fixada **AAD_** e usada para que o serviço de sincronização real seja executado como. Se instalar o Azure AD Connect num Controlador de Domínio, a conta é criada no domínio. A conta de serviço **AAD_** deve estar localizada no domínio se:
   - utiliza-se um servidor remoto que executa o servidor SQL
   - você usa um proxy que requer autenticação

![Conta de Serviço sincronizado](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma senha complexa e longa que não expira.

Esta conta é utilizada para armazenar as palavras-passe para as outras contas de forma segura. Estas outras contas são armazenadas encriptadas na base de dados. As chaves privadas para as chaves de encriptação estão protegidas com a encriptação de chave secreta de serviços criptográficos utilizando a API de Proteção de Dados do Windows (DPAPI).

Se utilizar um Servidor SQL completo, então a conta de serviço é o DBO da base de dados criada para o motor de sincronização. O serviço não funcionará como pretendido com quaisquer outras permissões. Também é criado um login SQL.

A conta também é concedida permissões a ficheiros, chaves de registo e outros objetos relacionados com o Motor Sync.

### <a name="azure-ad-connector-account"></a>Conta do Conector do Azure AD
É criada uma conta em Azure AD para uso do serviço de sincronização. Esta conta pode ser identificada pelo seu nome de exibição.

![Conta AD](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

O nome do servidor em que a conta é utilizada pode ser identificado na segunda parte do nome do utilizador. Na imagem, o nome do servidor é DC1. Se tiver servidores de encenação, cada servidor tem a sua própria conta.

A conta é criada com uma senha complexa e longa que não expira. É-lhe concedido um papel especial Contas de Sincronização de **Directórioque** que tem apenas permissões para executar tarefas de sincronização de diretórios. Esta função especial incorporada não pode ser concedida fora do assistente Azure AD Connect. O portal Azure mostra esta conta com a função **Utilizador.**

Existe um limite de 20 contas de serviço sincronizado em Azure AD. Para obter a lista das contas de serviço azure existentes no seu Azure AD, faça o seguinte Cmdlet Azure AD PowerShell:`Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Para remover as contas de serviço Azure AD não utilizadas, execute o seguinte cmdlet Azure AD PowerShell:`Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>Antes de poder utilizar os comandos powerShell acima, terá de instalar o [PowerShell de Diretório Ativo Azure para módulo graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) e ligar-se à sua instância de Azure AD utilizando [o Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

Para obter informações adicionais sobre como gerir ou redefinir a palavra-passe para a conta de Conector AD Azure ver [Gerir a conta Azure AD Connect](how-to-connect-azureadaccount.md)

## <a name="related-documentation"></a>Documentação relacionada
Caso não tenha lido a documentação sobre integração das suas identidades no local com o [Diretório Ativo Azure,](whatis-hybrid-identity.md)a tabela seguinte fornece links para tópicos relacionados.

|Tópico |Ligação|  
| --- | --- |
|Transferir o Azure AD Connect | [Transferir o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](how-to-connect-install-express.md)|
|Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)|
|Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verificar a instalação e atribuir licenças](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
