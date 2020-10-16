---
title: Azure AD Connect e privacidade do utilizador ! Microsoft Docs
description: Este documento descreve como obter a conformidade do RGPD com o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279572"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Privacidade do utilizador e Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Este artigo trata do Azure AD Connect e da privacidade do utilizador.  Para obter informações sobre a Azure AD Connect Health e a privacidade do utilizador consulte o artigo [aqui](reference-connect-health-user-privacy.md).

Melhorar a privacidade do utilizador para as instalações Azure AD Connect de duas formas:

1.  A pedido, extrair dados para uma pessoa e remover dados dessa pessoa das instalações
2.  Certifique-se de que nenhum dado é retido para além de 48 horas.

A equipa Azure AD Connect recomenda a segunda opção, uma vez que é muito mais fácil de implementar e manter.

Um servidor de sincronização AZure AD Connect armazena os seguintes dados de privacidade do utilizador:
1.  Dados sobre uma pessoa na base de **dados AZure AD Connect**
2.  Dados nos ficheiros de registo do **Windows Event** que podem conter informações sobre uma pessoa
3.  Dados nos **ficheiros de registo de instalação Azure AD Connect** que podem conter cerca de uma pessoa

Os clientes Azure AD Connect devem utilizar as seguintes diretrizes ao remover os dados do utilizador:
1.  Elimine o conteúdo da pasta que contém os ficheiros de registo de instalação Azure AD Connect regularmente – pelo menos a cada 48 horas
2.  Este produto também pode criar Registos de Eventos.  Para saber mais sobre os registos de registos de eventos, consulte [a documentação aqui.](/windows/win32/wes/windows-event-log)

Os dados sobre uma pessoa são automaticamente removidos da base de dados Azure AD Connect quando os dados dessa pessoa são removidos do sistema de origem de onde se originou. Não é necessária qualquer ação específica por parte dos administradores para ser compatível com o RGPD.  No entanto, requer que os dados do Azure AD Connect esteja sincronizado com a sua fonte de dados pelo menos de dois em dois dias.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Eliminar o conteúdo da pasta de ficheiros de registo de instalação Azure AD Connect
Verifique e elimine regularmente o conteúdo da pasta **c:\programdata\aadconnect** – exceto no ficheiro **PersistedState.Xml.** Este ficheiro mantém o estado da instalação anterior do Azure A Connect e é utilizado quando uma instalação de upgrade é executada. Este ficheiro não contém dados sobre uma pessoa e não deve ser eliminado.

>[!IMPORTANT]
>Não elimine o ficheiro PersistedState.xml.  Este ficheiro não contém nenhuma informação do utilizador e mantém o estado da instalação anterior.

Pode rever e eliminar estes ficheiros utilizando o Windows Explorer ou pode utilizar um script como o seguinte para executar as ações necessárias:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Agende este roteiro para ser executado a cada 48 horas
Utilize os seguintes passos para agendar o script a cada 48 horas.

1.  Guarde o script num ficheiro com a extensão **&#46;PS1**, depois abra o Painel de Controlo e clique em **Sistemas e Segurança**.
    ![Sistema](./media/reference-connect-user-privacy/gdpr2.png)

2.  No título Ferramentas Administrativas, clique em **Agendar Tarefas**.
    ![Tarefa](./media/reference-connect-user-privacy/gdpr3.png)
3.  No Agendador de Tarefas, clique no botão direito na **Biblioteca de Agendas de Tarefas** e clique em **Criar Tarefa Básica...**
4.  Introduza o nome para a nova tarefa e clique em **Seguinte**.
5.  Selecione **Daily** para o gatilho de tarefa e clique em **Seguinte**.
6.  Desaprote a recorrência para **2 dias** e clique em **Seguinte**.
7.  Selecione **Iniciar um programa** como ação e clique em **Seguinte**.
8.  Digite **PowerShell** na caixa para o Programa/script, e na caixa etiquetada **Adicione argumentos (opcional)**, insira o caminho completo para o script que criou anteriormente e, em seguida, clique em **Seguinte**.
9.  O ecrã seguinte mostra um resumo da tarefa que está prestes a criar. Verifique os valores e clique **em Terminar** para criar a tarefa.



## <a name="next-steps"></a>Passos seguintes
* [Reveja a política de privacidade da Microsoft no Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Saúde e Privacidade do Utilizador](reference-connect-health-user-privacy.md)