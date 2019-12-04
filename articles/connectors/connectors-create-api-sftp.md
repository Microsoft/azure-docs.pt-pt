---
title: Conectar-se à conta SFTP
description: Automatizar tarefas e processos que monitoram, criam, gerenciam, enviam e recebem arquivos para um servidor SFTP por meio de SSH usando o aplicativo lógico do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: divswa, klam, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: d0da98070fa8da5403677e1a67bda75456c74d80
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789278"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps"></a>Monitorar, criar e gerenciar arquivos SFTP usando o aplicativo lógico do Azure

> [!IMPORTANT]
> Use o [conector SFTP-SSH](../connectors/connectors-sftp-ssh.md) , pois o conector SFTP foi preterido. Você não pode mais selecionar gatilhos e ações de SFTP no designer de aplicativo lógico.

Para automatizar tarefas que monitoram, criamm, enviam e recebem arquivos em um servidor de [protocolo FTP seguro (SFTP)](https://www.ssh.com/ssh/sftp/) , você pode criar e automatizar fluxos de trabalho de integração usando os aplicativos lógicos do Azure e o conector SFTP. O SFTP é um protocolo de rede que fornece acesso a ficheiros, transferência de ficheiros e gestão de ficheiros através de qualquer fluxo de dados fiável. Aqui estão algumas tarefas de exemplo que você pode automatizar:

* Monitorar quando os arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, listar e excluir arquivos.
* Obter conteúdo do arquivo e metadados.
* Extraia arquivos mortos em pastas.

Você pode usar gatilhos que monitoram eventos em seu servidor SFTP e tornam a saída disponível para outras ações. Você pode usar ações que executam várias tarefas em seu servidor SFTP. Você também pode ter outras ações em seu aplicativo lógico usar a saída de ações de SFTP. Por exemplo, se você recuperar regularmente arquivos do seu servidor SFTP, poderá enviar alertas por email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou o conector do Outlook.com. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Limites

O conector SFTP manipula apenas os arquivos que são *50 MB ou menores* e não dá suporte ao [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). Para arquivos maiores, use o [conector SFTP-SSH](../connectors/connectors-sftp-ssh.md). Para diferenças entre o conector SFTP e o conector SFTP-SSH, examine [comparar SFTP-SSH versus SFTP](../connectors/connectors-sftp-ssh.md#comparison) no artigo SFTP-SSH.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Seu endereço de servidor SFTP e as credenciais de conta, que permitem que seu aplicativo lógico acesse sua conta SFTP. Para usar o protocolo [SSH (Secure Shell)](https://www.ssh.com/ssh/protocol/) , você também precisa ter acesso a uma chave privada SSH e à senha de chave privada SSH.

  > [!NOTE]
  >
  > O conector SFTP dá suporte a esses formatos de chave privada: OpenSSH, ssh.com e de saída
  >
  > Quando você estiver criando seu aplicativo lógico, depois de adicionar o gatilho SFTP ou a ação desejada, você precisará fornecer informações de conexão para seu servidor SFTP. 
  > Se você estiver usando uma chave privada SSH, certifique-se de ***copiar*** a chave do seu arquivo de chave privada SSH e ***Cole*** essa chave nos detalhes da conexão, ***não insira manualmente ou edite a chave***, o que pode causar falha na conexão. 
  > Para obter mais informações, consulte as etapas posteriores neste artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta SFTP. Para começar com um gatilho SFTP, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação SFTP, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **recorrência** .

## <a name="how-sftp-triggers-work"></a>Como funcionam os gatilhos do SFTP

Os gatilhos SFTP funcionam sondando o sistema de arquivos SFTP e procurando por qualquer arquivo que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o carimbo de data/hora quando os arquivos são alterados. Nesses casos, você precisa desabilitar esse recurso para que o gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Acesse **opções** > **preferências** > **transferir** > **Editar** > **preservar carimbo de data/hora** > **desabilitar** |
| FileZilla | Vá para **transferir** > **preservar carimbos de data/hora dos arquivos transferidos** > **desabilitar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se o novo arquivo está concluído e não parcialmente gravado. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo parcialmente gravado, o gatilho nota o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna imediatamente esse arquivo. O gatilho retorna o arquivo somente ao sondar o servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

## <a name="connect-to-sftp"></a>Conectar-se ao SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, digite "SFTP" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. Na caixa de pesquisa, digite "SFTP" como filtro. Na lista ações, selecione a ação desejada.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão.

   > [!IMPORTANT]
   >
   > Quando você inserir sua chave privada SSH na propriedade de **chave privada SSH** , siga estas etapas adicionais, que ajudam a garantir que você forneça o valor completo e correto para essa propriedade. 
   > Uma chave inválida faz com que a conexão falhe.

   Embora você possa usar qualquer editor de texto, aqui estão as etapas de exemplo que mostram como copiar e colar corretamente sua chave usando o notepad. exe como exemplo.

   1. Abra o arquivo de chave privada SSH em um editor de texto. Essas etapas usam o bloco de notas como exemplo.

   1. No menu **Editar** do bloco de notas, selecione **selecionar tudo**.

   1. Selecione **editar** > **cópia**.

   1. No gatilho SFTP ou na ação que você adicionou, Cole a chave *completa* que você copiou na propriedade de **chave privada SSH** , que dá suporte a várias linhas. ***Certifique-se de colar*** a chave. ***Não insira ou edite manualmente a chave***.

1. Quando você terminar de inserir os detalhes da conexão, escolha **criar**.

1. Forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho de SFTP: quando um arquivo é adicionado ou modificado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico quando um arquivo é adicionado ou alterado em um servidor SFTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e Obtém o conteúdo com base em se o conteúdo atende a uma condição especificada. Em seguida, você pode adicionar uma ação que obtém o conteúdo do arquivo e coloca esse conteúdo em uma pasta no servidor SFTP.

**Exemplo empresarial**: você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes. Você pode usar uma ação SFTP, como **obter conteúdo do arquivo** , para obter o conteúdo da ordem para processamento adicional e armazenar esse pedido em um banco de dados de pedidos.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>Ação SFTP: obter conteúdo

Essa ação Obtém o conteúdo de um arquivo em um servidor SFTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do arquivo deve atender. Se a condição for verdadeira, a ação que obtém o conteúdo poderá ser executada.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/sftpconnector/)do conector.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
