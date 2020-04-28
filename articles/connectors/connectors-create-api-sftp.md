---
title: Ligar à conta SFTP
description: Automatizar tarefas e processos que monitorizam, criam, gerem, enviam e recebem ficheiros para um servidor SFTP através do SSH utilizando aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: divswa, klam, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: d0da98070fa8da5403677e1a67bda75456c74d80
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789278"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorizar, criar e gerir ficheiros SFTP com Azure Logic Apps

> [!IMPORTANT]
> Utilize o [conector SFTP-SSH,](../connectors/connectors-sftp-ssh.md) uma vez que o conector SFTP é depreciado. Já não é possível selecionar gatilhos e ações da SFTP no Logic App Designer.

Para automatizar tarefas que monitorizam, criam, enviam e recebem ficheiros num servidor [secure File Transfer Protocol (SFTP),](https://www.ssh.com/ssh/sftp/) pode construir e automatizar fluxos de trabalho de integração utilizando apps da Lógica Azure e o conector SFTP. O SFTP é um protocolo de rede que fornece acesso a ficheiros, transferência de ficheiros e gestão de ficheiros através de qualquer fluxo de dados fiável. Aqui estão algumas tarefas de exemplo que pode automatizar:

* Monitorize quando os ficheiros forem adicionados ou alterados.
* Obtenha, crie, copie, atualize, e apague ficheiros.
* Obtenha conteúdo de ficheiros e metadados.
* Extrair arquivos para pastas.

Pode utilizar gatilhos que monitorizam eventos no seu servidor SFTP e disponibilizar a saída a outras ações. Pode utilizar ações que executam várias tarefas no seu servidor SFTP. Também pode ter outras ações na sua aplicação lógica, utilizar a saída a partir de ações SFTP. Por exemplo, se recuperar regularmente ficheiros do seu servidor SFTP, pode enviar alertas de e-mail sobre esses ficheiros e o seu conteúdo utilizando o conector do Office 365 Outlook ou Outlook.com conector. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limites

O conector SFTP trata apenas ficheiros com *50 MB ou menores* e não suporta [a reparte da mensagem](../logic-apps/logic-apps-handle-large-messages.md). Para ficheiros maiores, utilize o [conector SFTP-SSH](../connectors/connectors-sftp-ssh.md). Para as diferenças entre o conector SFTP e o conector SFTP-SSH, reveja [Compare SFTP-SSH contra SFTP](../connectors/connectors-sftp-ssh.md#comparison) no artigo SFTP-SSH.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O seu endereço de servidor SFTP e credenciais de conta, que permitem ao seu aplicativo lógico aceder à sua conta SFTP. Para utilizar o protocolo [Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) também precisa de acesso a uma chave privada SSH e à senha de chave privada SSH.

  > [!NOTE]
  >
  > O conector SFTP suporta estes formatos de chave privadas: OpenSSH, ssh.com e PuTTY
  >
  > Quando estiver a criar a sua aplicação lógica, depois de adicionar o gatilho ou ação SFTP que deseja, terá de fornecer informações de ligação para o seu servidor SFTP. 
  > Se estiver a utilizar uma chave privada SSH, certifique-se de ***copiar*** a chave do seu ficheiro de chave privado SSH e ***colar*** essa chave nos detalhes da ligação, ***não introduza manualmente ou edite a chave***, o que pode fazer com que a ligação falhe. 
  > Para mais informações, consulte os passos posteriores deste artigo.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SFTP. Para começar com um gatilho SFTP, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação SFTP, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **recorrência.**

## <a name="how-sftp-triggers-work"></a>Como o SFTP dispara

A SFTP desencadeia o trabalho através da sondagem do sistema de ficheiros SFTP e à procura de qualquer ficheiro que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem-lhe preservar a marca de tempo quando os ficheiros mudam. Nestes casos, tem de desativar esta funcionalidade para que o gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| Estação Winscp | Ir para **opções** > **Preferências** > **Transferência** > Editar**Editar** > Carimbo de**Tempo** > **Desativar** |
| FileZilla | Vá para **transferir** > **preservar selos de tempo dos ficheiros transferidos** > **Desativar** |
|||

Quando um gatilho encontra um novo ficheiro, o gatilho verifica se o novo ficheiro está completo e não parcialmente escrito. Por exemplo, um ficheiro pode ter alterações em andamento quando o gatilho verificar o servidor de ficheiros. Para evitar a devolução de um ficheiro parcialmente escrito, o gatilho assinala a marca ção do ficheiro que tem alterações recentes, mas não devolve imediatamente esse ficheiro. O gatilho devolve o ficheiro apenas quando voltar a votar no servidor. Às vezes, este comportamento pode causar um atraso que é até o dobro do intervalo de votação do gatilho.

## <a name="connect-to-sftp"></a>Ligar à SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "sftp" como filtro. Na lista de gatilhos, selecione o gatilho que deseja.

   -ou-

   Para aplicações lógicas existentes, sob o último passo em que pretende adicionar uma ação, escolha **novo passo**. Na caixa de pesquisa, introduza "sftp" como filtro. Na lista de ações, selecione a ação que deseja.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. Escolha o sinal**+** de mais () que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para a sua ligação.

   > [!IMPORTANT]
   >
   > Quando introduzir a sua chave privada SSH na propriedade **chave privada SSH,** siga estes passos adicionais, que ajudam a garantir que fornece o valor completo e correto para esta propriedade. 
   > Uma chave inválida faz com que a ligação falhe.

   Embora possa utilizar qualquer editor de texto, aqui estão os passos da amostra que mostram como copiar e colar corretamente a sua chave utilizando o Notepad.exe como exemplo.

   1. Abra o seu ficheiro de chave privada SSH num editor de texto. Estes passos utilizam o Bloco de Notas como exemplo.

   1. No menu **'Editar'** de notas, **selecione Selecione All**.

   1. Selecione **Editar** > **Cópia**.

   1. No gatilho ou ação SFTP adicionado, cola a chave *completa* que copiou na propriedade **chave privada SSH,** que suporta várias linhas. ***Certifique-se de colar*** a chave. ***Não introduza manualmente ou edite a tecla***.

1. Quando terminar de introduzir os detalhes da ligação, escolha **Criar**.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="examples"></a>Exemplos

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho SFTP: Quando um ficheiro é adicionado ou modificado

Este gatilho inicia um fluxo de trabalho de aplicação lógica quando um ficheiro é adicionado ou alterado num servidor SFTP. Por exemplo, pode adicionar uma condição que verifica o conteúdo do ficheiro e obtém o conteúdo com base no facto de o conteúdo cumprir uma condição especificada. Em seguida, pode adicionar uma ação que obtém o conteúdo do ficheiro e coloca esse conteúdo numa pasta no servidor SFTP.

**Exemplo da empresa:** Pode utilizar este gatilho para monitorizar uma pasta SFTP para novos ficheiros que representem as ordens do cliente. Em seguida, pode utilizar uma ação SFTP, como obter conteúdo de **ficheiros** para obter o conteúdo da encomenda para posterior processamento e guardar essa encomenda numa base de dados de encomendas.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Ação SFTP: Obter conteúdo

Esta ação obtém o conteúdo de um ficheiro num servidor SFTP. Assim, por exemplo, pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do ficheiro deve satisfazer. Se a condição for verdadeira, a ação que obtém o conteúdo pode ser executada.

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](/connectors/sftpconnector/)do conector .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
