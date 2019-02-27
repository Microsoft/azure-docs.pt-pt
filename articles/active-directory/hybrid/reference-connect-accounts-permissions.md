---
title: 'Azure AD Connect: Contas e permissões | Documentos da Microsoft'
description: Este tópico descreve as contas usadas e criado e as permissões necessárias.
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
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d145407331ed652f21510483b51a4617bf28e2fa
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879123"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: Contas e permissões

## <a name="accounts-used-for-azure-ad-connect"></a>Contas utilizadas para o Azure AD Connect

![](media/reference-connect-accounts-permissions/account5.png)

O Azure AD Connect utiliza as 3 contas para sincronizar informações do Windows Server Active Directory ou no local ao Azure Active Directory.  Estas contas são:

- **Conta do conector do DS AD**: utilizado para leitura/escrita informações para o Windows Server Active Directory

- **Conta de serviço do ADSync**: utilizada para executar o serviço de sincronização e aceder à base de dados SQL

- **Conta do Azure de conector AD**: utilizado para gravar informações para o Azure AD

Além desses três contas utilizadas para executar o Azure AD Connect, também terá as seguintes contas adicionais para instalar o Azure AD Connect.  Nomeadamente:

- **Conta de administrador local**: O administrador que está a instalar o Azure AD Connect e quem tem permissões de administrador local no computador.

- **Conta de administrador de empresa do AD DS**: Opcionalmente, é utilizado para criar a "conta de conector do AD DS" acima.

- **Conta de Administrador Global do AD do Azure**: utilizado para criar a conta do conector do Azure AD e configurar o Azure AD.

- **Conta do SA do SQL (opcional)**: utilizado para criar a base de dados ADSync ao utilizar a versão completa do SQL Server.  Este servidor de SQL pode ser local ou remoto para a instalação do Azure AD Connect.  Esta conta pode ser a mesma conta como o administrador de empresa.  Aprovisionamento da base de dados pode agora ser realizado fora de banda pelo administrador do SQL e, em seguida, instalado pelo administrador do Azure AD Connect com direitos de proprietário da base de dados.  Para obter informações sobre este veja [instalar o Azure AD Connect utilizando as permissões de administrador do SQL delegado](how-to-connect-install-sql-delegation.md)

## <a name="installing-azure-ad-connect"></a>A instalar o Azure AD Connect
O Assistente de instalação do Azure AD Connect oferece dois caminhos diferentes:

* As definições rápidas, o assistente necessita de mais privilégios.  Isto é, de modo que possa definir a sua configuração facilmente, sem necessidade de criar os utilizadores ou configurar as permissões.
* Em definições personalizadas, o assistente oferece mais opções e opções. No entanto, existem algumas situações em que precisa garantir que tem as permissões corretas por conta própria.



## <a name="express-settings-installation"></a>Definições de instalação rápida
Nas definições do Express, o Assistente de instalação faz o seguinte:

  - Credenciais de administrador de empresa do AD DS
  - Credenciais de Administrador Global do AD do Azure

### <a name="ad-ds-enterprise-admin-credentials"></a>Credenciais de administrador da empresa de DS do AD
A conta de administrador da empresa do AD DS é utilizada para configurar o Active Directory no local. Estas credenciais só são utilizadas durante a instalação e não são utilizadas após a conclusão da instalação. O administrador da empresa, não é o administrador de domínio, deverá certificar-se de que permissões no Active Directory podem ser definidas em todos os domínios.

Se estiver a atualizar do DirSync, as credenciais de Admins de empresa do AD DS são utilizadas para repor a palavra-passe para a conta utilizada pelo DirSync. Também precisa de credenciais de Administrador Global do Azure AD.

### <a name="azure-ad-global-admin-credentials"></a>Credenciais de Administrador Global do AD do Azure
Estas credenciais só são utilizadas durante a instalação e não são utilizadas após a conclusão da instalação. É utilizado para criar a conta de conector do Azure AD utilizada para sincronizar alterações para o Azure AD. A conta também permite a sincronização como uma funcionalidade no Azure AD.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>Conta do conector do DS AD permissões necessárias para as definições rápidas
A conta do conector do AD DS é criada para leitura e gravação para o Windows Server AD e tem as seguintes permissões quando criado pelas definições rápidas:

| Permissão | Utilizado para |
| --- | --- |
| <li>Replicar as alterações de diretório</li><li>Diretório de replicar muda tudo |Sincronização de hash de palavra-passe |
| Leitura/escrita todas as propriedades de utilizador |Importar e o Exchange híbrido |
| Leitura/escrita todas as propriedades iNetOrgPerson |Importar e o Exchange híbrido |
| Grupo de todas as propriedades de leitura/escrita |Importar e o Exchange híbrido |
| Entre em contato com todas as propriedades de leitura/escrita |Importar e o Exchange híbrido |
| Repor palavra-passe |Preparação para ativar a repetição de escrita de palavra-passe |

### <a name="express-installation-wizard-summary"></a>Resumo do Assistente de instalação rápida

![Instalação rápida](./media/reference-connect-accounts-permissions/express.png)

Segue-se um resumo das páginas de Assistente de instalação rápida, as credenciais de recolhidos, e o que são utilizadas.

| Página do Assistente | Credenciais recolhidas | Permissões necessárias | Utilizado para |
| --- | --- | --- | --- |
| N/A |Utilizador que executa o Assistente de instalação |Administrador do servidor local |<li>Cria a conta de serviço do ADSync que é utilizada para executar o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório do Azure AD |Função de administrador global no Azure AD |<li>Ativar a sincronização no diretório do Azure AD.</li>  <li>Criação da conta do conector do Azure AD que é utilizada para operações de sincronização em curso no Azure AD.</li> |
| Ligar ao AD DS |Credenciais do Active Directory no local |Membro do grupo Admins de empresa (EA) no Active Directory |<li>Cria a conta do conector do AD DS no Active Directory e concede permissões a ele. Isso criou a conta é utilizado para ler e escrever informações de diretório durante a sincronização.</li> |


## <a name="custom-installation-settings"></a>Definições de instalação personalizada

Com a instalação de definições personalizadas, o assistente oferece mais opções e opções. 

### <a name="custom-installation-wizard-summary"></a>Resumo do Assistente de instalação personalizada

Segue-se um resumo das páginas de Assistente de instalação personalizada, as credenciais de recolhidos, e o que são utilizadas.

![Instalação rápida](./media/reference-connect-accounts-permissions/customize.png)

| Página do Assistente | Credenciais recolhidas | Permissões necessárias | Utilizado para |
| --- | --- | --- | --- |
| N/A |Utilizador que executa o Assistente de instalação |<li>Administrador do servidor local</li><li>Se utilizar um SQL Server total, o utilizador tem de ser administrador de sistema (SA) no SQL Server</li> |Por predefinição, cria a conta local que é utilizada como a conta de serviço de motor de sincronização. A conta é criada apenas quando o administrador não especifica uma determinada conta. |
| Instalar serviços de sincronização, opção de conta de serviço |AD ou credenciais de conta de utilizador local |Utilizador, as permissões são concedidas pelo Assistente de instalação |Se o administrador especifica uma conta, esta conta é utilizada como a conta de serviço para o serviço de sincronização. |
| Ligar ao Azure AD |Credenciais de diretório do Azure AD |Função de administrador global no Azure AD |<li>Ativar a sincronização no diretório do Azure AD.</li>  <li>Criação da conta do conector do Azure AD que é utilizada para operações de sincronização em curso no Azure AD.</li> |
| Ligar os diretórios |Credenciais do Active Directory no local para cada floresta que está ligada ao Azure AD |As permissões dependem quais recursos ativa e pode ser encontrado em criar a conta do conector do AD DS |Esta conta é utilizada para ler e gravar informações de diretório durante a sincronização. |
| Servidores do AD FS |Para cada servidor na lista, o assistente recolhe as credenciais quando as credenciais de início de sessão do utilizador que executa o assistente não são suficientes para ligar |Administrador de domínio |Instalação e configuração da função de servidor do AD FS. |
| Servidores de proxy de aplicações Web |Para cada servidor na lista, o assistente recolhe as credenciais quando as credenciais de início de sessão do utilizador que executa o assistente não são suficientes para ligar |Administrador local no computador de destino |Instalação e configuração da função de servidor do WAP. |
| Credenciais de confiança do proxy |Credenciais de confiança de serviço de Federação (as credenciais de proxy utiliza para registrar um certificado de confiança do FS |Conta de domínio que seja um administrador local do servidor do AD FS |Inscrição inicial do certificado de confiança FS WAP. |
| Página de conta de serviço FS AD, "Utilizar uma opção de conta de utilizador de domínio" |Credenciais de conta de utilizador do AD |Utilizador de domínio |A conta de utilizador do AD cujas credenciais são fornecidas é utilizada como a conta de início de sessão do serviço do AD FS. |

### <a name="create-the-ad-ds-connector-account"></a>Criar a conta do conector do AD DS

>[!IMPORTANT]
>Um novo módulo do PowerShell ADSyncConfig.psm1 nomeado foi introduzida com compilação **1.1.880.0** (lançado em Agosto de 2018) que inclui uma coleção de cmdlets para o ajudar a configurar as permissões corretas do Active Directory para o Azure AD DS Conta de conector.
>
>Para obter mais informações consulte [do Azure AD Connect: Configurar as permissões de conta do AD DS conector](how-to-connect-configure-ad-ds-connector-account.md)

A conta que especificar no **ligar os seus diretórios** página tem de estar presente no Active Directory antes da instalação.  O Azure AD Connect na versão 1.1.524.0 e posterior, tem a opção para permitir que o Assistente do Azure AD Connect crie a **conta de conector do AD DS** utilizado para ligar ao Active Directory.  

Também tem de ter as permissões necessárias concedidas. O Assistente de instalação não verifica que as permissões e quaisquer problemas, apenas são encontrados durante a sincronização.

As permissões que necessita depende das funcionalidades opcionais ativa. Se tiver vários domínios, tem de ser concedidas as permissões para todos os domínios na floresta. Se não ativar qualquer um destes recursos, a predefinição **utilizador de domínio** permissões são suficientes.

| Funcionalidade | Permissões |
| --- | --- |
| funcionalidade do MS-DS-ConsistencyGuid |Permissões de escrita para o atributo ms-DS-ConsistencyGuid documentado em [conceitos de Design - usando o ms-DS-ConsistencyGuid como sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronização de hash de palavra-passe |<li>Replicar as alterações de diretório</li>  <li>Diretório de replicar muda tudo |
| Implementação híbrida do Exchange |Permissões de escrita para os atributos documentados em [repetição de escrita do Exchange híbrido](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) para utilizadores, grupos e contactos. |
| Pasta de públicas de correio do Exchange |Permissões de leitura para os atributos documentados em [pastas públicas de correio Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) para as pastas públicas. | 
| Repetição de escrita de palavras-passe |Permissões de escrita para os atributos documentados em [introdução à gestão de palavra-passe](../authentication/howto-sspr-writeback.md) para os utilizadores. |
| Repetição de escrita do dispositivo |Permissões concedidas com um script do PowerShell, conforme descrito em [repetição de escrita do dispositivo](how-to-connect-device-writeback.md). |
| Repetição de escrita do grupo |Permite-lhe a repetição de escrita **grupos do Office 365** para uma floresta com o Exchange instalado.  Para obter mais informações, consulte [repetição de escrita do grupo](how-to-connect-preview.md#group-writeback).|

## <a name="upgrade"></a>Atualizar
Ao atualizar de uma versão do Azure AD Connect para uma nova versão, tem as seguintes permissões:

>[!IMPORTANT]
>A partir da compilação 1.1.484, o Azure AD Connect introduziu um bug de regressão que necessita de permissões de administrador do sistema para atualizar a base de dados SQL.  Esse bug é corrigido em compilação 1.1.647.  Se estiver a atualizar essa compilação, terá permissões de administrador do sistema.  Permissões de dbo não são suficientes.  Se tentar atualizar o Azure AD Connect sem ter permissões de administrador do sistema, a atualização irá falhar e o Azure AD Connect deixarão de funcionar corretamente depois disso.  A Microsoft está ciente disso e está a trabalhar para corrigir este problema.


| Principal | Permissões necessárias | Utilizado para |
| --- | --- | --- |
| Utilizador que executa o Assistente de instalação |Administrador do servidor local |Os binários de atualização. |
| Utilizador que executa o Assistente de instalação |Membro de ADSyncAdmins |Faça as alterações a regras de sincronização e outra configuração. |
| Utilizador que executa o Assistente de instalação |Se utilizar um SQL server total: DBO (ou semelhante) da base de dados de motor de sincronização |Efetue alterações de nível de base de dados, como a atualizar as tabelas com novas colunas. |

## <a name="more-about-the-created-accounts"></a>Mais informações sobre as contas criadas
### <a name="ad-ds-connector-account"></a>Conta do conector do DS AD
Se utilizar as definições rápidas, é criada uma conta no Active Directory que é utilizada para sincronização. A conta criada está localizada no domínio de raiz de floresta no contêiner usuários e tem o seu nome prefixadas com **msol _**. A conta é criada com uma palavra-passe muito complexa que não expire. Se tiver uma política de palavra-passe no seu domínio, certifique-se de que muito e palavras-passe complexas teria permissão para esta conta.

![Conta AD](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

Se utilizar definições personalizadas, em seguida, é responsável por criar a conta antes de iniciar a instalação.  Consulte a criar a conta do conector do AD DS.

### <a name="adsync-service-account"></a>Conta de serviço do ADSync
O serviço de sincronização pode ser executados sob contas diferentes. Pode ser executado sob uma **conta de serviço Virtual** (VSA), uma **conta de serviço gerida de grupo** (gMSA/sMSA) ou uma conta de utilizador normal. As opções suportadas foram alteradas com o 2017 versão de Abril do Connect ao fazer uma instalação de raiz. Se atualizar a partir de uma versão anterior do Azure AD Connect, não estão disponíveis as opções adicionais.

| Tipo de conta | Opção de instalação | Descrição |
| --- | --- | --- |
| [Conta de serviço virtual](#virtual-service-account) | Abril de 2017 Express e personalizadas e mais tarde | Esta é a opção utilizada para todas as instalações do express, exceto para instalações num controlador de domínio. Para personalizada, é a opção predefinida, a menos que outra opção é utilizada. |
| [Conta de serviço geridas de grupo](#group-managed-service-account) | Personalizado, Abril de 2017 e posterior | Se utilizar um SQL server remoto, em seguida, recomendamos que utilize uma conta de serviço geridas de grupo. |
| [Conta de utilizador](#user-account) | Abril de 2017 Express e personalizadas e mais tarde | Uma conta de utilizador, o prefixo AAD_ só é criada durante a instalação quando instalado no Windows Server 2008 e quando instalado num controlador de domínio. |
| [Conta de utilizador](#user-account) | Março de 2017 Express e personalizadas e versões anteriores | Uma conta local AAD_ o prefixo é criada durante a instalação. Ao utilizar a instalação personalizada, é possível especificar outra conta. |

Se usar Connect com uma compilação de 2017 Março ou anteriormente, em seguida, deverá não repor a palavra-passe na conta do serviço, uma vez que o Windows destrói as chaves de encriptação por motivos de segurança. Não é possível alterar a conta para qualquer outra conta sem reinstalar o Azure AD Connect. Se atualiza para uma compilação de 2017 Abril ou posterior, em seguida, é suportada alterar a palavra-passe da conta de serviço, mas não pode alterar a conta utilizada.

> [!Important]
> A conta de serviço só pode ser definida na primeira instalação. Não é suportada para alterar a conta de serviço após a conclusão da instalação.

Esta é uma tabela de predefinição, as opções de recomendada e suportadas para a conta de serviço de sincronização.

Legenda:

- **Negrito** indica a opção predefinida e na maioria dos casos, a opção recomendada.
- *Itálico* indica a opção recomendada quando não é a opção predefinida.
- 2008 - opção de predefinida quando instalado no Windows Server 2008
- Não-negrito - opção com suporte
- Conta local - da conta de utilizador Local no servidor
- Conta de domínio - conta de utilizador de domínio
- sMSA - [conta de serviço gerida autónoma](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA - [conta de serviço gerida de grupo](https://technet.microsoft.com/library/hh831782.aspx)

| | LocalDB</br>Express | LocalDB/LocalSQL</br>Personalizado | SQL remoto</br>Personalizado |
| --- | --- | --- | --- |
| **máquina autónomo/grupo de trabalho** | Não suportado | **VSA**</br>Conta local (2008)</br>Conta local |  Não suportado |
| **máquina associados a um domínio** | **VSA**</br>Conta local (2008) | **VSA**</br>Conta local (2008)</br>Conta local</br>Conta do domínio</br>sMSA,gMSA | **gMSA**</br>Conta do domínio |
| **Controlador de domínio** | **Conta de domínio** | *gMSA*</br>**Conta de domínio**</br>sMSA| *gMSA*</br>**Conta de domínio**|

#### <a name="virtual-service-account"></a>Conta de serviço virtual
Uma conta de serviço virtual é um tipo especial de conta que não tem uma palavra-passe e é gerido pelo Windows.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

O VSA destina-se a ser utilizada com cenários em que o motor de sincronização e SQL estão no mesmo servidor. Se usar SQL remoto, em seguida, recomendamos que utilize uma conta de serviço de gerida de grupo em vez disso.

Esta funcionalidade requer o Windows Server 2008 R2 ou posterior. Se instalar o Azure AD Connect no Windows Server 2008, a instalação utilizará um [conta de utilizador](#user-account) em vez disso.

#### <a name="group-managed-service-account"></a>Conta de serviço geridas de grupo
Se utilizar um SQL server remoto, em seguida, recomendamos a utilização uma **conta de serviço gerida de grupo**. Para obter mais informações sobre como preparar o Active Directory para a conta de serviço gerida de grupo, consulte [descrição geral a contas de serviço do grupo geridas](https://technet.microsoft.com/library/hh831782.aspx).

Para utilizar esta opção, sobre o [instalar os componentes necessários](how-to-connect-install-custom.md#install-required-components) página, selecione **utilizar uma conta de serviço existente**e selecione **conta de serviço gerida**.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
Também é possível para utilizar um [conta de serviço gerida de autónomo](https://technet.microsoft.com/library/dd548356.aspx). No entanto, estes só podem ser utilizados na máquina local e não existe nenhum benefício a utilizá-los sobre a conta de serviço virtual predefinida.

Esta funcionalidade requer o Windows Server 2012 ou posterior. Se precisa usar um sistema operacional mais antigo e utilizar o SQL remoto, tem de utilizar um [conta de utilizador](#user-account).

#### <a name="user-account"></a>Conta de utilizador
Uma conta de serviço local é criada pelo Assistente de instalação (a menos que especifique a conta a utilizar nas definições personalizadas). A conta tem o prefixo **AAD_** e utilizado para o serviço de sincronização real para executar como. Se instalar o Azure AD Connect num controlador de domínio, a conta é criada no domínio. O **AAD_** conta de serviço tem de estar localizada no domínio se:
   - utilizar um servidor remoto executando o SQL server
   - utilizar um proxy que requer autenticação

![Conta de serviço de sincronização](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

A conta é criada com uma palavra-passe muito complexa que não expire.

Esta conta é utilizada para armazenar as palavras-passe para as outras contas de maneira segura. Estas outras contas palavras-passe é armazenados encriptados na base de dados. As chaves privadas para as chaves de encriptação são protegidas com a encriptação de chave secreta de serviços criptográficos através da API de proteção de dados do Windows (DPAPI).

Se utilizar um SQL Server total, em seguida, a conta de serviço é ao DBO da base de dados criada para o motor de sincronização. O serviço não irão funcionar como pretendido com quaisquer outras permissões. Também é criado um início de sessão do SQL.

A conta também tem permissões para ficheiros, chaves do Registro e outros objetos relacionados com o motor de sincronização.

### <a name="azure-ad-connector-account"></a>Conta de conector AD do Azure
É criada uma conta no Azure AD para utilizar o serviço de sincronização. Esta conta pode ser identificada pelo respetivo nome de exibição.

![Conta AD](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

O nome do servidor que a conta é utilizada no pode ser identificado na segunda parte do nome do utilizador. Na imagem, o nome do servidor é DC1. Se tiver servidores de teste, cada servidor tem sua própria conta.

A conta é criada com uma palavra-passe muito complexa que não expire. Ele é concedido uma função especial **contas de sincronização do** que tem apenas permissões para executar tarefas de sincronização de diretório. Não é possível conceder esta função incorporada especial fora do Assistente do Azure AD Connect. O portal do Azure mostra esta conta com a função **utilizador**.

Existe um limite de 20 contas de serviço de sincronização no Azure AD. Para obter a lista de contas de serviço do Azure AD existentes no seu Azure AD, execute o seguinte cmdlet do PowerShell do Azure AD: `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

Para remover o AD do Azure não utilizado contas de serviço, execute o seguinte cmdlet do PowerShell do Azure AD: `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

## <a name="related-documentation"></a>Documentação relacionada
Se não leu a documentação sobre [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md), a tabela seguinte fornece ligações para tópicos relacionados.

|Tópico |Ligação|  
| --- | --- |
|Transferir o Azure AD Connect | [Transferir o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](how-to-connect-install-express.md)|
|Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](./how-to-connect-install-custom.md)|
|Atualização do DirSync | [Atualizar da ferramenta de sincronização do Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|Após a instalação | [Verificar a instalação e atribuir licenças](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
