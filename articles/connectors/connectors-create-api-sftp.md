---
title: Ligar à conta SFTP (Deprecada)
description: Automatizar tarefas e processos que monitorizem, criem, gerem, enviem e recebam ficheiros para um servidor SFTP utilizando apps Azure Logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 70fb956af7ff45c7b54f04d7ed441ec39f9d80a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673812"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>Monitore, crie e gere ficheiros SFTP em Azure Logic Apps

> [!IMPORTANT]
> Utilize o [conector SFTP-SSH](../connectors/connectors-sftp-ssh.md) à medida que o conector SFTP estiver prectado. Já não é possível selecionar gatilhos e ações SFTP no Logic App Designer.

Para automatizar tarefas que monitorizem, criem, enviem e recebam ficheiros num servidor [de Protocolo de Transferência de Ficheiros Seguro (SFTP),](https://www.ssh.com/ssh/sftp/) pode construir e automatizar fluxos de trabalho de integração utilizando apps Azure Logic e o conector SFTP. O SFTP é um protocolo de rede que fornece acesso a ficheiros, transferência de ficheiros e gestão de ficheiros através de qualquer fluxo de dados fiável. Aqui estão algumas tarefas de exemplo que pode automatizar:

* Monitorize quando os ficheiros forem adicionados ou alterados.
* Obtenha, crie, copie, atualize, liste e elimine ficheiros.
* Obtenha conteúdo de ficheiro e metadados.
* Extrair arquivos para pastas.

Pode utilizar gatilhos que monitorizem eventos no seu servidor SFTP e disponibilizem a saída para outras ações. Pode utilizar ações que executam várias tarefas no seu servidor SFTP. Também pode ter outras ações na sua aplicação lógica que utilizam a saída das ações da SFTP. Por exemplo, se recuperar regularmente ficheiros do seu servidor SFTP, pode enviar alertas de e-mail sobre esses ficheiros e o seu conteúdo utilizando o conector Do Office 365 Outlook ou o conector Outlook.com. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limites

O conector SFTP só lida com ficheiros com *50 MB ou menores* e não suporta [mensagens em pedaços](../logic-apps/logic-apps-handle-large-messages.md). Para ficheiros maiores, utilize o [conector SFTP-SSH](../connectors/connectors-sftp-ssh.md). Para obter diferenças entre o conector SFTP e o conector SFTP-SSH, [reveja compare SFTP-SSH versus SFTP](../connectors/connectors-sftp-ssh.md#comparison) no artigo SFTP-SSH.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O endereço do servidor SFTP e as credenciais de conta, que permitem que a sua aplicação lógica aceda à sua conta SFTP. Para utilizar o protocolo [Secure Shell (SSH),](https://www.ssh.com/ssh/protocol/) também precisa de acesso a uma chave privada SSH e à palavra-passe privada SSH.

  > [!NOTE]
  >
  > O conector SFTP suporta estes formatos chave privados: OpenSSH, ssh.com e PuTTY
  >
  > Quando estiver a criar a sua aplicação lógica, depois de adicionar o gatilho SFTP ou a ação que pretende, terá de fornecer informações de ligação para o seu servidor SFTP. 
  > Se estiver a utilizar uma chave privada SSH, certifique-se de ***que copia*** a chave do seu ficheiro de chave privada SSH e ***cole*** essa chave nos detalhes da ligação, ***não introduza ou edite manualmente a chave***, o que pode fazer com que a ligação falhe. 
  > Para mais informações, consulte os passos posteriores neste artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SFTP. Para começar com um gatilho SFTP, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação SFTP, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="how-sftp-triggers-work"></a>Como os gatilhos SFTP funcionam

A SFTP desencadeia o trabalho através da sondagem do sistema de ficheiros SFTP e à procura de qualquer ficheiro que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o tempo de tempo quando os ficheiros mudam. Nestes casos, tem de desativar esta funcionalidade para que o seu gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| Winscp | Ir a **Opções**  >  **Preferências**  >    >  **Transferir Editar**  >  **Preservar horários Desativar**  >   |
| FileZilla | Ir para **transferir**  >  **limites tempos de ficheiros transferidos Desativar**  >   |
|||

Quando um gatilho encontra um novo ficheiro, o gatilho verifica se o novo ficheiro está completo e não parcialmente escrito. Por exemplo, um ficheiro pode ter alterações em andamento quando o gatilho verifica o servidor de ficheiros. Para evitar a devolução de um ficheiro parcialmente escrito, o gatilho anota o tempo de tempo para o ficheiro que tem alterações recentes, mas não devolve imediatamente esse ficheiro. O gatilho devolve o ficheiro apenas quando volta a sondar o servidor. Às vezes, este comportamento pode causar um atraso que é até o dobro do intervalo de votação do gatilho.

## <a name="connect-to-sftp"></a>Ligar ao SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza "sftp" como filtro. Na lista de gatilhos, selecione o gatilho que pretende.

   -ou-

   Para aplicações lógicas existentes, sob o último passo onde pretende adicionar uma ação, escolha **Novo passo**. Na caixa de pesquisa, introduza "sftp" como filtro. Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. Escolha o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Forneça os detalhes necessários para a sua ligação.

   > [!IMPORTANT]
   >
   > Quando introduzir a sua chave privada SSH na propriedade **chave privada SSH,** siga estes passos adicionais, que ajudam a garantir que fornece o valor completo e correto para esta propriedade. 
   > Uma chave inválida faz com que a ligação falhe.

   Embora possa utilizar qualquer editor de texto, aqui estão os passos de amostra que mostram como copiar e colar corretamente a sua chave usando Notepad.exe como exemplo.

   1. Abra o seu ficheiro de chave privada SSH num editor de texto. Estes passos usam o Bloco de Notas como exemplo.

   1. No menu **Notepad Edit,** **selecione Select All**.

   1. Selecione   >  **Editar Copy**.

   1. No gatilho ou ação SFTP que adicionou, cole a chave *completa* que copiou para a propriedade **chave privada SSH,** que suporta várias linhas. **_Certifique-se de colar_*_ a chave. _* Não _introduza manualmente ou edite a chave_**.

1. Quando terminar de inserir os detalhes da ligação, escolha **Criar**.

1. Forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="examples"></a>Exemplos

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho SFTP: Quando um ficheiro é adicionado ou modificado

Este gatilho inicia um fluxo de trabalho de aplicações lógicas quando um ficheiro é adicionado ou alterado num servidor SFTP. Por exemplo, pode adicionar uma condição que verifica o conteúdo do ficheiro e obtém o conteúdo com base no facto de o conteúdo cumprir uma condição especificada. Em seguida, pode adicionar uma ação que obtém o conteúdo do ficheiro e coloca esse conteúdo numa pasta no servidor SFTP.

**Exemplo da empresa**: Pode utilizar este gatilho para monitorizar uma pasta SFTP para novos ficheiros que representem as ordens dos clientes. Em seguida, pode utilizar uma ação SFTP, como **obter conteúdo de ficheiros,** para obter o conteúdo da encomenda para posterior processamento e armazenar essa encomenda numa base de dados de encomendas.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Ação SFTP: Obter conteúdo

Esta ação obtém o conteúdo de um ficheiro num servidor SFTP. Assim, por exemplo, pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do ficheiro deve cumprir. Se a condição for verdadeira, a ação que obtém o conteúdo pode ser executada.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](../data-factory/connector-sftp.md)do conector .

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)