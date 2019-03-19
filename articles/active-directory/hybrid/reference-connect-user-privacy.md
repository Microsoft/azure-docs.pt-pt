---
title: Privacidade do Azure de Connect e o utilizador de AD | Documentos da Microsoft
description: Este documento descreve como obter a conformidade do GDPR com o Azure AD Connect.
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
ms.openlocfilehash: 6f5d3125b7b77e8ce7a943f640c44615049ab160
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096355"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Privacidade do utilizador e do Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Este artigo trata da privacidade do Azure AD Connect e o utilizador.  Para obter informações sobre privacidade do Azure AD Connect Health e utilizador, consulte o artigo [aqui](reference-connect-health-user-privacy.md).

Melhore a privacidade dos utilizadores para o Azure AD Connect instalações de duas formas:

1.  Mediante solicitação, extrair dados para uma pessoa e remover dados de que a pessoa das instalações
2.  Certifique-se de que nenhum dado é mantido para além de 48 horas.

A equipa do Azure AD Connect recomenda a segunda opção, pois é muito mais fácil de implementar e manter.

Um servidor de sincronização do Azure AD Connect armazena os dados de privacidade do utilizador seguintes:
1.  Dados sobre um objeto person no **base de dados do Azure AD Connect**
2.  Dados do **registo de eventos do Windows** arquivos que podem conter informações sobre uma pessoa
3.  Dados do **ficheiros de registo de instalação do Azure AD Connect** que pode conter sobre uma pessoa

Clientes do Azure AD Connect devem utilizar as seguintes diretrizes quando a remoção de dados de utilizador:
1.  Elimine o conteúdo da pasta que contém os ficheiros de registo de instalação do Azure AD Connect regularmente – pelo menos a cada 48 horas
2.  Este produto também pode criar registos de eventos.  Para saber mais sobre os registos de registos de eventos, consulte a [a documentação aqui presente](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Dados sobre uma pessoa são removidos automaticamente da base de dados do Azure AD Connect quando dados essa pessoa são removidos do sistema de origem em que foi gerado. É necessária nenhuma ação específica de administradores para estar em conformidade do GDPR.  No entanto, ele exige que os dados do Azure AD Connect estão sincronizados com a sua origem de dados, pelo menos, em dois dias.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Eliminar o conteúdo de pasta do ficheiro de registo do instalação do Azure AD Connect
Regularmente Verifique e elimine o conteúdo da **c:\programdata\aadconnect** pasta – exceto para o **PersistedState.Xml** ficheiro. Este ficheiro mantém o estado da instalação anterior do Azure A Connect e é utilizado quando é efetuada uma instalação de atualização. Este ficheiro não contém quaisquer dados sobre uma pessoa e não deve ser eliminado.

>[!IMPORTANT]
>Não elimine o ficheiro de PersistedState.xml.  Este ficheiro não contêm nenhuma informação de utilizador e mantém o estado da instalação anterior.

Pode rever e eliminar estes ficheiros ao utilizar o Explorador do Windows, ou pode usar um script semelhante ao seguinte para executar as ações necessárias:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Agendar este script para executar cada 48 horas
Utilize os seguintes passos para agendar o script seja executado a cada 48 horas.

1.  Guarde o script num arquivo com a extensão  **&#46;PS1**, em seguida, abra o painel de controlo e clique em **sistemas e segurança**.
    ![Sistema](./media/reference-connect-user-privacy/gdpr2.png)

2.  Sob o cabeçalho de ferramentas administrativas, clique em **agendamento de tarefas**.
    ![Tarefa](./media/reference-connect-user-privacy/gdpr3.png)
3.  No agendador de tarefas, clique em **biblioteca de agendamento de tarefas** e clique em **tarefa básica de criar...**
4.  Introduza o nome para a nova tarefa e clique em **seguinte**.
5.  Selecione **diária** para o acionador de tarefa e clique em **próxima**.
6.  Definir periodicidade **2 dias** e clique em **próxima**.
7.  Selecione **iniciar um programa** como a ação e clique em **próxima**.
8.  Tipo **PowerShell** na caixa para o programa/script e, na caixa com a etiqueta **Adicione argumentos (opcional)**, introduza o caminho completo para o script que criou anteriormente, em seguida, clique em **próxima**.
9.  O ecrã seguinte mostra um resumo da tarefa que está prestes a criar. Verifique os valores e clique em **concluir** para criar a tarefa.



## <a name="next-steps"></a>Passos Seguintes
* [Rever a política de Privacy da Microsoft no Centro de fidedignidade](https://www.microsoft.com/trustcenter)
* [Estado de funcionamento e a privacidade dos utilizadores do Azure AD Connect](reference-connect-health-user-privacy.md)
