---
title: 'Azure AD Connect: Atualizar do DirSync | Documentos da Microsoft'
description: Saiba como atualizar do DirSync para o Azure AD Connect. Este artigo descreve os passos para atualizar do DirSync para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: baf52da7-76a8-44c9-8e72-33245790001c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f2d9a7c8cfbfc4fb56ff8fba3c65ae9a7925830
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852975"
---
# <a name="azure-ad-connect-upgrade-from-dirsync"></a>Azure AD Connect: Atualização do DirSync
O Azure AD Connect é o sucessor do DirSync. Encontrará neste tópico as forma de atualizar a partir do DirSync. Estes passos não funcionam para atualizar a partir de outra versão do Azure AD Connect ou a partir do Azure AD Sync.

Antes de começar a instalar o Azure AD Connect, certifique-se de que [transferir o Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) e concluir os pré-requisitos indicados [do Azure AD Connect: Pré-requisitos de hardware e](how-to-connect-install-prerequisites.md). Em particular, pretende ler sobre o seguinte, uma vez que estas áreas são diferentes do DirSync:

* A versão necessária do .NET e o PowerShell. É necessário que estejam versões mais recentes no servidor do que era necessário no DirSync.
* A configuração de servidor proxy. Se utilizar um servidor proxy para a Internet, esta definição tem de ser configurada antes de atualizar. O DirSync utilizou sempre o servidor proxy configurado para o utilizador que o instalava; em vez disso, o Azure AD Connect utiliza definições do computador.
* Os URLs que devem estar abertos no servidor proxy. Para cenários básicos que também sejam suportados pelo DirSync, os requisitos são os mesmos. Se pretender utilizar qualquer uma das novas funcionalidades incluídas no Azure AD Connect, alguns URLs têm de estar abertos.

> [!NOTE]
> Assim que tiver ativado o novo servidor do Azure AD Connect para iniciar a sincronização das alterações para o Azure AD, não tem de reverter para o DirSync ou o Azure AD Sync. A mudança do Azure AD Connect para clientes legados, incluindo o DirSync e o Azure AD Sync não é suportada e poderá originar problemas, tais como a perda de dados no Azure AD.

Se não estiver a atualizar do DirSync, consulte a documentação relacionada para outros cenários.

## <a name="upgrade-from-dirsync"></a>Atualização do DirSync
Dependendo da sua implementação atual do DirSync, existem várias opções para a atualização. Se o tempo estimado para a atualização for inferior a três horas, recomenda-se efetuar uma atualização no local. Se o tempo estimado para a atualização for superior a três horas, recomenda-se efetuar uma implementação paralela noutro servidor. Estima-se que, se tiver mais de 50 000 objetos, a atualização demorará mais de três horas.

| Cenário |
| --- |
| [Atualização no local](#in-place-upgrade) |
| [Implementação paralela](#parallel-deployment) |

> [!NOTE]
> Quando planear atualizar do DirSync para o Azure AD Connect, não desinstale o DirSync antes da atualização. O Azure AD Connect lê e migra a configuração do DirSync e desinstala-o depois inspecionar o servidor.

**Atualização no local**  
O assistente apresenta o tempo esperado para concluir a atualização. Esta estimativa baseia-se no pressuposto de que demora três horas a concluir uma atualização para uma base de dados com 50 000 objetos (utilizadores, contactos e grupos). Se o número de objetos na base de dados for inferior a 50 000, o Azure AD Connect recomenda uma atualização no local. Se decidir continuar, as definições atuais são aplicadas automaticamente durante a atualização e o servidor retoma automaticamente a sincronização ativa.

Se pretender efetuar uma migração da configuração e efetuar uma implementação paralela, então pode ignorar a recomendação de atualização no local. Poderá, por exemplo, aproveitar a oportunidade para atualizar o hardware e o sistema operativo. Para obter mais informações, consulte a secção [implementação paralela](#parallel-deployment).

**Implementação paralela**  
Se tiver mais de 50 000 objetos, então é recomendada uma implementação paralela. Esta implementação evita assim que os utilizadores sofram atrasos operacionais. A instalação do Azure AD Connect tenta estimar o período de indisponibilidade devido à atualização, mas se tiver atualizado anteriormente o DirSync, a sua experiência pessoal será, provavelmente, o melhor guia.

### <a name="supported-dirsync-configurations-to-be-upgraded"></a>Configurações suportadas do DirSync a atualizar
As seguintes alterações de configuração são suportadas no DirSync atualizado:

* Filtragem de domínios e de UOs
* ID Alternativo (UPN)
* Sincronização de palavra-passe e definições híbridas do Exchange
* A floresta/domínio e as definições do Azure AD
* Filtragem baseada nos atributos de utilizador

Não é possível atualizar a alteração seguinte. Se tiver esta configuração, a atualização será bloqueada:

* Alterações não suportadas do DirSync, por exemplo, atributos removidos e a utilização de uma DLL personalizada

![Atualização bloqueada](./media/how-to-dirsync-upgrade-get-started/analysisblocked.png)

Nestes casos, a recomendação é instalar um novo servidor do Azure AD Connect em [modo de teste](how-to-connect-sync-staging-server.md) e verificar a antiga configuração do DirSync e a nova configuração do Azure AD Connect. Volte a aplicar as alterações utilizando uma configuração personalizada, conforme descrito em [Configuração personalizada da Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md).

Não é possível obter as palavras-passe utilizadas pelo DirSync para as contas de serviço, e não são migradas. Estas palavras-passe serão repostas durante a atualização.

### <a name="high-level-steps-for-upgrading-from-dirsync-to-azure-ad-connect"></a>Passos de alto nível para atualizar do DirSync para o Azure AD Connect.
1. Bem-vindo ao Azure AD Connect
2. Análise da configuração atual do DirSync
3. Recolher a palavra-passe de administrador global do Azure AD
4. Recolher as credenciais para uma conta de administrador da empresa (utilizada apenas durante a instalação do Azure AD Connect)
5. Instalação do Azure AD Connect
   * Desinstalar o DirSync (ou desativar temporariamente)
   * Instalar o Azure AD Connect
   * Opcionalmente, iniciar a sincronização

São necessários passos adicionais se:

* Estiver a utilizar atualmente o SQL Server total, local ou remoto
* Tiver mais de 50 000 objetos no âmbito da sincronização

## <a name="in-place-upgrade"></a>Atualização no local
1. Inicie o instalador do Azure AD Connect (MSI).
2. Leia e aceite os termos de licenciamento e o aviso de privacidade.  
   ![Bem-vindo ao Azure AD](./media/how-to-dirsync-upgrade-get-started/Welcome.png)
3. Clique em seguinte para iniciar uma análise da instalação existente do DirSync.  
   ![Analisar a instalação existente da Sincronização de Diretórios](./media/how-to-dirsync-upgrade-get-started/Analyze.png)
4. Quando a análise estiver concluída, vê as recomendações sobre como proceder.  
   * Se utilizar o SQL Server Express e tiver menos de 50 000 objetos, é apresentado o ecrã seguinte:  
     ![Análise concluída, pronto para atualizar a partir do DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReady.png)
   * Se utilizar um SQL Server completo para DirSync, verá em vez disso esta página:  
     ![Análise concluída, pronto para atualizar a partir do DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
     São apresentadas as informações sobre o servidor de base de dados existente do SQL Server que está a ser utilizado pelo DirSync. Efetue os ajustes adequados, se necessário. Clique em **Seguinte** para continuar a instalação.
   * Se tiver mais de 50.000 objetos, verá em vez disso este ecrã:  
     ![Análise concluída, pronto para atualizar a partir do DirSync](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
     Para continuar com uma atualização no local, clique na caixa de verificação junto a esta mensagem: **Continue a atualização do DirSync neste computador.**
     Para fazer, em vez disso, uma [implementação paralela](#parallel-deployment), exporte e mova para o novo servidor a configuração do DirSync.
5. Forneça a palavra-passe da conta que utiliza atualmente para ligar ao Azure AD. Tem de ser a conta utilizada atualmente pelo DirSync.  
   ![Introduzir as credenciais do Azure AD](./media/how-to-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
   Se receber um erro e tiver problemas com a conectividade, consulte [Resolver problemas de conectividade](tshoot-connect-connectivity.md).
6. Forneça uma conta de administrador da empresa do Active Directory.  
   ![Introduzir as credenciais do ADDS](./media/how-to-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Está agora pronto para a configuração. Quando clica em **Atualizar**, o DirSync é desinstalado e o Azure AD Connect é configurado e começa a sincronização.  
   ![Preparado para configurar](./media/how-to-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Após a conclusão da instalação, termine e inicie a sessão novamente no Windows antes de utilizar o Synchronization Service Manager, o Editor de Regras de Sincronização ou tentar efetuar outras alterações de configuração.

## <a name="parallel-deployment"></a>Implementação paralela
### <a name="export-the-dirsync-configuration"></a>Exportar a configuração do DirSync
**Implementação paralela com mais de 50 000 objetos**

Se tiver mais de 50 000 objetos, a instalação do Azure AD Connect recomenda uma implementação paralela.

É apresentado um ecrã semelhante ao seguinte:  
![Análise completa](./media/how-to-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Se pretender continuar com a implementação paralela, terá de efetuar os seguintes passos:

* Clique no botão **Exportar definições**. Ao instalar o Azure AD Connect num servidor separado, estas definições são migradas do DirSync atual para a nova instalação do Azure AD Connect.

Assim que as definições forem exportadas com êxito, pode sair do assistente do Azure AD Connect no servidor do DirSync. Continuar com o passo seguinte para instalar o Azure AD Connect num servidor separado

**Implementação paralela com menos de 50 000 objetos**

Se tiver menos de 50 000 objetos mas quiser, mesmo assim, fazer uma implementação paralela, efetue o seguinte:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando vir o ecrã **Bem-vindo ao Azure AD Connect**, saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra uma linha de comandos.
4. Da localização de instalação do Azure AD Connect (predefinição: C:\Programas\Microsoft Azure Active Directory Connect) execute o seguinte comando: `AzureADConnect.exe /ForceExport`.
5. Clique no botão **Exportar definições**. Ao instalar o Azure AD Connect num servidor separado, estas definições são migradas do DirSync atual para a nova instalação do Azure AD Connect.

![Análise completa](./media/how-to-dirsync-upgrade-get-started/forceexport.png)

Assim que as definições forem exportadas com êxito, pode sair do assistente do Azure AD Connect no servidor do DirSync. Avance para o passo seguinte para instalar o Azure AD Connect num servidor separado.

### <a name="install-azure-ad-connect-on-separate-server"></a>Instalar o Azure AD Connect num servidor separado
Ao instalar o Azure AD Connect num servidor novo, o pressuposto é que pretende efetuar uma instalação de raiz do Azure AD Connect. Uma vez que pretende utilizar a configuração do DirSync, há alguns passos adicionais:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando vir o ecrã **Bem-vindo ao Azure AD Connect**, saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra uma linha de comandos.
4. Da localização de instalação do Azure AD Connect (predefinição: C:\Programas\Microsoft Azure Active Directory Connect) execute o seguinte comando: `AzureADConnect.exe /migrate`.
   O assistente de instalação do Azure AD Connect arranca e apresenta o seguinte ecrã:  
   ![Introduzir as credenciais do Azure AD](./media/how-to-dirsync-upgrade-get-started/ImportSettings.png)
5. Selecione o ficheiro de definições que exportou na instalação do DirSync.
6. Configure as opções avançadas, incluindo:
   * Uma localização de instalação personalizada para o Azure AD Connect.
   * Uma instância existente do SQL Server (predefinição: O Azure AD Connect instala o SQL Server 2012 Express). Não utilize a mesma instância de base de dados do servidor do DirSync.
   * Uma conta de serviço utilizada para ligar ao SQL Server (se a base de dados do SQL Server for remota, esta conta tem de ser uma conta de serviço de domínio).
     Estas opções podem ser vistas neste ecrã:  
     ![Introduzir as credenciais do Azure AD](./media/how-to-dirsync-upgrade-get-started/advancedsettings.png)
7. Clique em **Seguinte**.
8. Na página **Preparado para configurar**, deixe marcada a opção **Inicie o processo de sincronização assim que a configuração for concluída**. O servidor está agora no [modo de teste](how-to-connect-sync-staging-server.md), para que as alterações não sejam exportadas para o Azure AD.
9. Clique em **Instalar**.
10. Após a conclusão da instalação, termine e inicie a sessão novamente no Windows antes de utilizar o Synchronization Service Manager, o Editor de Regras de Sincronização ou tentar efetuar outras alterações de configuração.

> [!NOTE]
> É iniciada a sincronização entre o Windows Server Active Directory e o Azure Active Directory, mas as alterações não são exportadas para o Azure AD. Só uma ferramenta de sincronização de cada vez pode exportar ativamente alterações. Este estado chama-se [modo de teste](how-to-connect-sync-staging-server.md).

### <a name="verify-that-azure-ad-connect-is-ready-to-begin-synchronization"></a>Verificar se o Azure AD Connect está pronto para iniciar a sincronização
Para verificar se o Azure AD Connect está pronto para substituir o DirSync, tem de abrir o **Synchronization Service Manager** no grupo **Azure AD Connect** do menu Iniciar.

Na aplicação, aceda ao separador **Operações**. Neste separador, confirme se as seguintes operações foram concluídas:

* Importar no Conector AD
* Importar no Conector Azure AD
* Sincronização completa no Conector AD
* Sincronização completa no Conector Azure AD

![Importação e sincronização concluídas](./media/how-to-dirsync-upgrade-get-started/importsynccompleted.png)

Consulte o resultado destas operações e certifique-se de que não contém erros.

Se pretender ver e inspecionar as alterações que estão prestes a ser exportadas para o Azure AD, deverá ler como verificar a configuração em [modo de teste](how-to-connect-sync-staging-server.md). Efetue as alterações de configuração necessárias até já não ver nada inesperado.

Está pronto para mudar do DirSync para o Azure AD quando concluir estes passos e estiver satisfeito com o resultado.

### <a name="uninstall-dirsync-old-server"></a>Desinstalar o DirSync (servidor antigo)
* Em **Programas e funcionalidades**, localize a **Ferramenta de sincronização do Windows Azure Active Directory**
* Desinstalar a **Ferramenta de sincronização do Windows Azure Active Directory**
* A desinstalação pode levar até 15 minutos para ficar concluída.

Se preferir desinstalar o DirSync mais tarde, pode também desligar temporariamente o servidor ou desativar o serviço. Caso algo corra mal, este método permite-lhe reativá-lo. No entanto, não é esperado que o passo seguinte falhe. Portanto, tal não deverá ser necessário.

Com o DirSync desinstalado ou desativado, deixa de existir um servidor ativo a exportar para o Azure AD. O passo seguinte para ativar o Azure AD Connect tem de ser concluído para que quaisquer alterações no Active Directory no local continuem a ser sincronizadas para o Azure AD.

### <a name="enable-azure-ad-connect-new-server"></a>Ativar o Azure AD Connect (novo servidor)
Após a instalação, voltar a abrir Azure AD Connect irá permitir-lhe efetuar alterações de configuração adicionais. Inicie o **Azure AD Connect** a partir do menu Iniciar ou do atalho no ambiente de trabalho. Certifique-se de que não está a tentar executar novamente a instalação do MSI.

Deverá ver o seguinte:  
![Tarefas adicionais](./media/how-to-dirsync-upgrade-get-started/AdditionalTasks.png)

* Selecione **Configurar o modo de teste**.
* Desative o teste desmarcando a caixa de verificação **Modo de teste ativado**.

![Introduzir as credenciais do Azure AD](./media/how-to-dirsync-upgrade-get-started/configurestaging.png)

* Clique no botão **Seguinte**
* Na página de confirmação, clique no botão **instalar**.

O Azure AD Connect é agora o seu servidor ativo e não tem de mudar para o servidor do DirSync existente.

## <a name="next-steps"></a>Passos Seguintes
Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](how-to-connect-post-installation.md).

Saiba mais sobre estas novas funcionalidades que foram ativadas com a instalação: [A atualização automática](how-to-connect-install-automatic-upgrade.md), [impedir eliminações acidentais](how-to-connect-sync-feature-prevent-accidental-deletes.md), e [Azure AD Connect Health](how-to-connect-health-sync.md).

Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](how-to-connect-sync-feature-scheduler.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
