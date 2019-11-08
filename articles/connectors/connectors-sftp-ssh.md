---
title: Conectar-se ao servidor SFTP com SSH-aplicativos lógicos do Azure
description: Automatizar tarefas que monitoram, criamm, gerenciam, enviam e recebem arquivos para um servidor SFTP usando SSH e aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/18/2019
tags: connectors
ms.openlocfilehash: a48ba0d2d691314a1ca7c91ac7ae27b62fbb379b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825246"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorar, criar e gerenciar arquivos SFTP usando SSH e aplicativos lógicos do Azure

Para automatizar tarefas que monitoram, criem, enviem e recebam arquivos em um servidor de [protocolo FTP seguro (SFTP)](https://www.ssh.com/ssh/sftp/) usando o protocolo [SSH (Secure Shell)](https://www.ssh.com/ssh/protocol/) , você pode criar e automatizar fluxos de trabalho de integração usando o aplicativo lógico do Azure e o SFTP-SSH conector. O SFTP é um protocolo de rede que fornece acesso a ficheiros, transferência de ficheiros e gestão de ficheiros através de qualquer fluxo de dados fiável. Aqui estão algumas tarefas de exemplo que você pode automatizar:

* Monitorar quando os arquivos são adicionados ou alterados.
* Obter, criar, copiar, renomear, atualizar, listar e excluir arquivos.
* Crie pastas.
* Obter conteúdo do arquivo e metadados.
* Extraia arquivos mortos em pastas.

Você pode usar gatilhos que monitoram eventos em seu servidor SFTP e tornam a saída disponível para outras ações. Você pode usar ações que executam várias tarefas em seu servidor SFTP. Você também pode ter outras ações em seu aplicativo lógico usar a saída de ações de SFTP. Por exemplo, se você recuperar regularmente arquivos do seu servidor SFTP, poderá enviar alertas por email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou o conector do Outlook.com. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md)

Para obter diferenças entre o conector SFTP-SSH e o conector SFTP, examine a seção [comparar SFTP-SSH versus SFTP](#comparison) mais adiante neste tópico.

## <a name="limits"></a>Limites

* Por padrão, as ações de SFTP-SSH podem ler ou gravar arquivos que são de *1 GB ou menores* , mas apenas em partes de *15 MB* por vez. Para lidar com arquivos com mais de 15 MB, as ações SFTP-SSH dão suporte ao [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md), exceto para a ação copiar arquivo, que pode manipular apenas 15 arquivos. A ação **obter conteúdo do arquivo** usa implicitamente o agrupamento de mensagens.

* Os gatilhos SFTP-SSH não dão suporte ao agrupamento. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 15 MB ou menores. Para obter arquivos com mais de 15 MB, siga este padrão em vez disso:

  * Use um gatilho SFTP-SSH que retorne Propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente Propriedades)** .

  * Siga o gatilho com a ação SFTP-SSH **Get file Content** , que lê o arquivo completo e usa implicitamente o agrupamento de mensagens.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparar SFTP-SSH versus SFTP

Aqui estão outras diferenças principais entre o conector SFTP-SSH e o conector SFTP em que o conector SFTP-SSH tem estes recursos:

* Usa a [biblioteca SSH.net](https://github.com/sshnet/SSH.NET), que é uma biblioteca de Secure Shell de código aberto (SSH) que dá suporte ao .net.

* Por padrão, as ações de SFTP-SSH podem ler ou gravar arquivos que são de *1 GB ou menores* , mas apenas em partes de *15 MB* por vez. Para lidar com arquivos com mais de 15 MB, as ações do SFTP-SSH podem usar o [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). Para carregar arquivos grandes, você também precisa de permissões de leitura e gravação. No entanto, a ação copiar arquivo dá suporte a apenas 15 arquivos, pois essa ação não dá suporte ao agrupamento de mensagens. Os gatilhos SFTP-SSH não dão suporte ao agrupamento.

* Fornece a ação **criar pasta** , que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação **Renomear arquivo** , que renomeia um arquivo no servidor SFTP.

* Armazena em cache a conexão com o servidor SFTP *por até 1 hora*, o que melhora o desempenho e reduz o número de tentativas na conexão com o servidor. Para definir a duração desse comportamento de cache, edite a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na configuração de SSH em seu servidor SFTP.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Seu endereço de servidor SFTP e as credenciais de conta, que permitem que seu aplicativo lógico acesse sua conta SFTP. Você também precisa de acesso a uma chave privada SSH e à senha de chave privada SSH. Para usar o Agrupamento ao carregar arquivos grandes, você precisa de permissões de leitura e gravação.

  > [!IMPORTANT]
  >
  > O conector SFTP-SSH dá suporte *apenas* a esses formatos de chave privada, algoritmos e impressões digitais:
  >
  > * **Formatos de chave privada**: chaves RSA (Rivest Shamir Adleman) e DSA (algoritmo de assinatura digital) nos formatos OpenSSH e SSH.com. Se sua chave privada estiver no formato de arquivo de reversões (. PPK), primeiro [converta a chave para o formato de arquivo OpenSSH (. pem)](#convert-to-openssh).
  >
  > * **Algoritmos de criptografia**: des-EDE3-CBC, des-EDE3-CFB, DES-CBC, AES-128-CBC, aes-192-CBC e AES-256-CBC
  >
  > * **Impressão digital**: MD5
  >
  > Depois de adicionar o gatilho SFTP-SSH ou a ação que você deseja ao seu aplicativo lógico, você precisa fornecer informações de conexão para seu servidor SFTP. Ao fornecer sua chave privada SSH para essa conexão, ***não insira ou edite manualmente a chave***, o que pode causar falha na conexão. Em vez disso, certifique-se de ***copiar a chave*** de seu arquivo de chave privada SSH e ***Cole*** essa chave nos detalhes da conexão. 
  > Para obter mais informações, consulte a seção [conectar-se ao SFTP com SSH](#connect) posteriormente neste artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta SFTP. Para começar com um gatilho SFTP-SSH, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação SFTP-SSH, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **recorrência** .

## <a name="how-sftp-ssh-triggers-work"></a>Como os gatilhos de SFTP-SSH funcionam

SFTP-os gatilhos SSH funcionam sondando o sistema de arquivos SFTP e procurando por qualquer arquivo que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o carimbo de data/hora quando os arquivos são alterados. Nesses casos, você precisa desabilitar esse recurso para que o gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Acesse **opções** > **preferências** > **transferir** > **Editar** > **preservar carimbo de data/hora** > **desabilitar** |
| FileZilla | Vá para **transferir** > **preservar carimbos de data/hora dos arquivos transferidos** > **desabilitar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se o novo arquivo está concluído e não parcialmente gravado. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo parcialmente gravado, o gatilho nota o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna imediatamente esse arquivo. O gatilho retorna o arquivo somente ao sondar o servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Converter chave baseada em saída para OpenSSH

Se sua chave privada estiver no formato de reversões, que usa a extensão de nome de arquivo. PPK (chave privada de saída), primeiro converta a chave para o formato OpenSSH, que usa a extensão de nome de arquivo. PEM (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Sistema operacional baseado em UNIX

1. Se as ferramentas de saída ainda não estiverem instaladas no sistema, faça isso agora, por exemplo:

   `sudo apt-get install -y putty`

1. Execute este comando, que cria um arquivo que você pode usar com o conector SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Por exemplo:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>SO Windows

1. Se você ainda não fez isso, [Baixe a ferramenta mais recente do gerador de geração (puttygen. exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)e, em seguida, inicie a ferramenta.

1. Nessa tela, selecione **carregar**.

   ![Selecione "carregar"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Navegue até o arquivo de chave privada no formato de saída e selecione **abrir**.

1. No menu **conversões** , selecione **Exportar chave OpenSSH**.

   ![Selecione "Exportar chave OpenSSH"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Salve o arquivo de chave privada com a extensão de nome de arquivo `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Conectar-se ao SFTP com SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, digite "SFTP SSH" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **nova etapa**. Na caixa de pesquisa, digite "SFTP SSH" como filtro. Na lista ações, selecione a ação desejada.

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão.

   > [!IMPORTANT]
   >
   > Quando você inserir sua chave privada SSH na propriedade de **chave privada SSH** , siga estas etapas adicionais, que ajudam a garantir que você forneça o valor completo e correto para essa propriedade. Uma chave inválida faz com que a conexão falhe.

   Embora você possa usar qualquer editor de texto, aqui estão as etapas de exemplo que mostram como copiar e colar corretamente sua chave usando o notepad. exe como exemplo.

   1. Abra o arquivo de chave privada SSH em um editor de texto. Essas etapas usam o bloco de notas como exemplo.

   1. No menu **Editar** do bloco de notas, selecione **selecionar tudo**.

   1. Selecione **editar** > **cópia**.

   1. No gatilho SFTP-SSH ou na ação que você adicionou, Cole a chave *completa* que você copiou na propriedade de **chave privada SSH** , que dá suporte a várias linhas.  ***Certifique-se de colar*** a chave. ***Não insira ou edite manualmente a chave***.

1. Quando você terminar de inserir os detalhes da conexão, escolha **criar**.

1. Agora, forneça os detalhes necessários para o gatilho ou ação selecionado e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP-gatilho SSH: quando um arquivo é adicionado ou modificado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico quando um arquivo é adicionado ou alterado em um servidor SFTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e Obtém o conteúdo com base em se o conteúdo atende a uma condição especificada. Em seguida, você pode adicionar uma ação que obtém o conteúdo do arquivo e coloca esse conteúdo em uma pasta no servidor SFTP.

**Exemplo empresarial**: você pode usar esse gatilho para monitorar uma pasta SFTP para novos arquivos que representam pedidos de clientes. Você pode usar uma ação SFTP, como **obter conteúdo do arquivo** , para obter o conteúdo da ordem para processamento adicional e armazenar esse pedido em um banco de dados de pedidos.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP-ação SSH: obter conteúdo usando o caminho

Essa ação Obtém o conteúdo de um arquivo em um servidor SFTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do arquivo deve atender. Se a condição for verdadeira, a ação que obtém o conteúdo poderá ser executada.

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](/connectors/sftpconnector/)do conector.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
