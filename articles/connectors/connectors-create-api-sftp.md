---
title: Ligar à conta do SFTP - Azure Logic Apps | Documentos da Microsoft
description: Automatizar tarefas e processos que monitorizarem, criarem, gerirem, enviarem e recebem ficheiros para um servidor SFTP através de SSH com o Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.topic: article
tags: connectors
ms.date: 10/26/2018
ms.openlocfilehash: 42e1ef3e311633f9631163bc9d3df212b608ef3a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60450765"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorizar, criar e gerir ficheiros de SFTP ao utilizar o Azure Logic Apps

Para automatizar as tarefas que monitorizarem, criarem, enviarem e recebem arquivos num [proteger o protocolo SFTP (File Transfer)](https://www.ssh.com/ssh/sftp/) servidor, que pode criar e automatizar fluxos de trabalho de integração com o Azure Logic Apps e o conector do SFTP. SFTP é um protocolo de rede que fornece acesso a ficheiros, a transferência de ficheiros e a gestão de ficheiros através de qualquer fluxo de dados fiável. Seguem-se algumas tarefas de exemplo que pode automatizar: 

* Monitor de quando os ficheiros são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e eliminar ficheiros.
* Obter conteúdo do ficheiro e metadados.
* Extraia os arquivos para pastas.

Pode usar acionadores que monitorar eventos em seu servidor SFTP e disponibilizar a saída para outras ações. Pode utilizar ações que executar diversas tarefas no seu servidor SFTP. Pode também ter outras ações na sua aplicação lógica a utilizar a saída de ações de SFTP. Por exemplo, se recuperar regularmente ficheiros a partir do seu servidor SFTP, pode enviar alertas por e-mail sobre esses arquivos e seu conteúdo com o conector do Outlook do Office 365 ou o conector do Outlook.com.
Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limits

* Ações de SFTP podem ler ou gravar arquivos que estão *50 MB ou mais pequeno* a menos que utilize [mensagem divisão na ações](../logic-apps/logic-apps-handle-large-messages.md), que permitem-lhe exceder este limite. Atualmente, os acionadores SFTP não suportam a segmentação.

* Para os ficheiros *até 1 GB*, utilize o [conector do SFTP-SSH](../connectors/connectors-sftp-ssh.md).

* Para os ficheiros *maior do que 1 GB*, utilize o SFTP-SSH conector adição [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md).

Para outras diferenças entre o conector do SFTP e o conector SFTP-SSH, veja [comparar SFTP-SSH versus SFTP](../connectors/connectors-sftp-ssh.md#comparison) no artigo SFTP-SSH.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>. 

* Os SFTP endereço e a conta de credenciais do servidor, que permitem que a aplicação de lógica aceder à sua conta do SFTP. Para utilizar o [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protocolo, também tem acesso a uma chave privada SSH e a senha da chave privada SSH. 

  > [!NOTE]
  > 
  > O conector do SFTP oferece suporte a esses formatos de chave privados: OpenSSH, ssh.com e PuTTY
  > 
  > Quando estiver a criar a aplicação lógica, depois de adicionar o acionador SFTP ou a ação que pretende, terá de fornecer informações de ligação para o seu servidor SFTP. 
  > Se estiver a utilizar uma chave privada SSH, certifique-se de que ***cópia*** a chave do seu ficheiro de chave privada, e ***cole*** essa chave os detalhes de ligação, ***manualmente não introduzir ou editar a chave***, que pode fazer com que a ligação falha. 
  > Para obter mais informações, veja os passos mais adiante neste artigo.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação de lógica onde pretende aceder à sua conta do SFTP. Para começar com um acionador SFTP [criar uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação de SFTP, inicie a aplicação lógica com outro acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-sftp"></a>Ligue-se ao SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para aplicações de lógica em branco, na caixa de pesquisa, introduza "sftp" como o filtro. Abaixo da lista de disparadores, selecione o acionador que pretende. 

   -ou-

   Para logic apps existentes, sob a última etapa em que pretende adicionar uma ação, escolha **novo passo**. 
   Na caixa de pesquisa, introduza "sftp" como o filtro. 
   Abaixo da lista de ações, selecione a ação que pretende.

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição ( **+** ) que é apresentada e, em seguida, selecione **adicionar uma ação**.

1. Forneça os detalhes necessários para a sua ligação.

   > [!IMPORTANT] 
   >
   > Quando inserir a chave privada SSH a **chave privada SSH** propriedade, siga estes passos adicionais, que ajudam a tornar-se de que forneça o valor completo e correto para esta propriedade. 
   > Uma chave inválida faz com que a ligação falha.
   
   Apesar de poder utilizar qualquer editor de texto, aqui estão os passos de exemplo que mostram como copiar e colar a chave ao utilizar Notepad.exe como um exemplo de corretamente.
    
   1. Abra o ficheiro de chave privado SSH num editor de texto. 
   Estes passos utilizam o bloco de notas do exemplo.

   1. Do bloco de notas **edite** menu, selecione **Selecionar tudo**.

   1. Selecione **edite** > **cópia**.

   1. No acionador SFTP ou ação que adicionou, cole a *concluída* chave que copiou para o **chave privada SSH** propriedade, que oferece suporte a várias linhas. 
   ***Certifique-se de que cole*** a chave. ***Não introduza manualmente ou editar a chave de***.

1. Quando terminar introduzir os detalhes de ligação, escolha **criar**.

1. Forneça os detalhes necessários para o seu acionador selecionado ou a ação e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="trigger-limits"></a>Limites de Acionador

O trabalho de acionadores SFTP, o sistema de ficheiros SFTP de consulta e está à procura de qualquer ficheiro que foi alterado desde a última consulta. Algumas ferramentas permitem-lhe preservar o carimbo de hora, quando os ficheiros alterados. Nestes casos, tem de desativar esta funcionalidade para que o acionador pode trabalhar. Seguem-se algumas definições comuns:

| Cliente SFTP | Ação | 
|-------------|--------| 
| Winscp | Aceda a **opções** > **preferências** > **transferir** > **editar**  >  **Preservar timestamp** > **desativar** |
| FileZilla | Aceda a **transferência** > **preservar os carimbos de ficheiros transferidos** > **desativar** | 
||| 

Quando um acionador localiza um novo ficheiro, o acionador verifica que o novo ficheiro é completa e não parcialmente escrito. Por exemplo, um ficheiro pode ter as alterações em curso quando o acionador verifica o servidor de ficheiros. Para evitar o retorno de um arquivo parcialmente escrito, o acionador anota o carimbo de hora para o ficheiro que tem alterações recentes, mas não retorna imediatamente esse arquivo. O acionador devolver o ficheiro apenas quando consulta o servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o acionador intervalo de consulta. 

Quando solicitar o conteúdo do ficheiro, acionadores não obtêm ficheiros superior a 50 MB. Para obter os ficheiros mais de 50 MB, siga este padrão: 

* Utilizar um acionador que retorna as propriedades do ficheiro, tal como **quando um ficheiro é adicionado ou modificado (propriedades apenas)** .

* Siga o acionador com uma ação que lê o arquivo completo, como **obter conteúdo do ficheiro através do caminho**, e ter a ação utilizar [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="examples"></a>Exemplos

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Acionador SFTP: Quando um ficheiro é adicionado ou modificado

Este acionador é iniciado um fluxo de trabalho de aplicação lógica quando um ficheiro é adicionado ou alterado num servidor SFTP. Por exemplo, pode adicionar uma condição que verifica o conteúdo do arquivo e obtém o conteúdo com base em se o conteúdo cumpre uma condição especificada. Em seguida, pode adicionar uma ação que obtém o conteúdo do arquivo e coloca esses conteúdos numa pasta no servidor SFTP. 

**Exemplo de Enterprise**: Pode utilizar este acionador para monitorizar uma pasta SFTP para novos ficheiros que representam as encomendas de cliente. Em seguida, pode utilizar como uma ação de SFTP **obter conteúdo do ficheiro** , de modo a obter o conteúdo do pedido para processamento adicional e armazenar essa ordem numa base de dados de encomendas.

Quando solicitar o conteúdo do ficheiro, acionadores não obtêm ficheiros superior a 50 MB. Para obter os ficheiros mais de 50 MB, siga este padrão: 

* Utilizar um acionador que retorna as propriedades do ficheiro, tal como **quando um ficheiro é adicionado ou modificado (propriedades apenas)** .

* Siga o acionador com uma ação que lê o arquivo completo, como **obter conteúdo do ficheiro através do caminho**, e ter a ação utilizar [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md).

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Ação de SFTP: Obter o conteúdo

Esta ação obtém o conteúdo de um arquivo num servidor SFTP. Por exemplo, pode adicionar o acionador do exemplo anterior e uma condição que o conteúdo do ficheiro tem de cumprir. Se a condição for verdadeira, pode executar a ação que obtém o conteúdo. 

Quando solicitar o conteúdo do ficheiro, acionadores não obtêm ficheiros superior a 50 MB. Para obter os ficheiros mais de 50 MB, siga este padrão: 

* Utilizar um acionador que retorna as propriedades do ficheiro, tal como **quando um ficheiro é adicionado ou modificado (propriedades apenas)** .

* Siga o acionador com uma ação que lê o arquivo completo, como **obter conteúdo do ficheiro através do caminho**, e ter a ação utilizar [mensagem segmentação](../logic-apps/logic-apps-handle-large-messages.md).

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](/connectors/sftpconnector/).

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
