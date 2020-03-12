---
title: Ligue-se ao servidor SFTP com SSH
description: Automatizar tarefas que monitorizam, criam, gerem, enviam e recebem ficheiros para um servidor SFTP utilizando apps lógicas SSH e Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, klam, logicappspm
ms.topic: article
ms.date: 03/7/2020
tags: connectors
ms.openlocfilehash: d4ab7425c967d3a176c0a576d0be38ece1701b8b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128408"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorize, crie e gere os ficheiros SFTP utilizando aplicações lógicas SSH e Azure

Para automatizar tarefas que monitorizam, criam, enviam e recebem ficheiros num servidor [secure File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) utilizando o protocolo Secure Shell [(SSH),](https://www.ssh.com/ssh/protocol/) pode construir e automatizar fluxos de trabalho de integração utilizando aplicações da Lógica Azure e o conector SFTP-SSH. O SFTP é um protocolo de rede que fornece acesso a ficheiros, transferência de ficheiros e gestão de ficheiros através de qualquer fluxo de dados fiável. Aqui estão algumas tarefas de exemplo que pode automatizar:

* Monitorize quando os ficheiros forem adicionados ou alterados.
* Obtenha, crie, copie, mude o nome, atualize, e apague ficheiros.
* Criar pastas.
* Obtenha conteúdo de ficheiros e metadados.
* Extrair arquivos para pastas.

Pode utilizar gatilhos que monitorizam eventos no seu servidor SFTP e disponibilizar a saída a outras ações. Pode utilizar ações que executam várias tarefas no seu servidor SFTP. Também pode ter outras ações na sua aplicação lógica, utilizar a saída a partir de ações SFTP. Por exemplo, se recuperar regularmente ficheiros do seu servidor SFTP, pode enviar alertas de e-mail sobre esses ficheiros e o seu conteúdo utilizando o conector do Office 365 Outlook ou Outlook.com conector. Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md)

Para as diferenças entre o conector SFTP-SSH e o conector SFTP, reveja a secção [Compare SFTP-SSH contra SFTP](#comparison) mais tarde neste tópico.

## <a name="limits"></a>Limites

* As ações SFTP-SSH que suportam [a chunking](../logic-apps/logic-apps-handle-large-messages.md) podem lidar com ficheiros até 1 GB, enquanto as ações SFTP-SSH que não suportam a chunking podem lidar com ficheiros até 50 MB. Embora o tamanho do pedaço padrão seja de 15 MB, este tamanho pode mudar dinamicamente, a partir de 5 MB e aumentando gradualmente para o máximo de 50 MB, com base em fatores como latência da rede, tempo de resposta do servidor, e assim por diante.

  > [!NOTE]
  > Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  Pode anular este comportamento adaptativo quando [especifica um tamanho constante](#change-chunk-size) para usar. Este tamanho pode variar entre 5 MB e 50 MB. Por exemplo, suponha que tenha um ficheiro de 45 MB e uma rede que possa suportar esse tamanho de ficheiro sem latência. Chunking adaptativo resulta em várias chamadas, em vez de uma chamada. Para reduzir o número de chamadas, pode tentar definir um tamanho de 50 MB. Em cenários diferentes, se a sua aplicação lógica estiver a cronometrar, por exemplo, ao utilizar pedaços de 15 MB, pode tentar reduzir o tamanho para 5 MB.

  O tamanho do pedaço está associado a uma ligação, o que significa que você pode usar a mesma conexão para ações que suportam chunking e, em seguida, para ações que não suportam chunking. Neste caso, o tamanho do pedaço para ações que não suportam os pedaços varia entre 5 MB e 50 MB. Esta tabela mostra quais as ações SFTP-SSH que suportam a chunking:

  | Ação | Suporte de chunking | Suporte de tamanho de pedaço de substituição |
  |--------|------------------|-----------------------------|
  | **Arquivo de cópia** | Não | Não aplicável |
  | **Criar ficheiro** | Sim | Sim |
  | **Criar pasta** | Não aplicável | Não aplicável |
  | **Eliminar ficheiro** | Não aplicável | Não aplicável |
  | **Extrair arquivo para pasta** | Não aplicável | Não aplicável |
  | **Obtenha conteúdo de ficheiro** | Sim | Sim |
  | **Obtenha conteúdo de ficheiro usando o caminho** | Sim | Sim |
  | **Obtenha metadados de ficheiros** | Não aplicável | Não aplicável |
  | **Obtenha metadados de ficheiros usando caminho** | Não aplicável | Não aplicável |
  | **Lista rés-da-lista na pasta** | Não aplicável | Não aplicável |
  | **Arquivo de renome** | Não aplicável | Não aplicável |
  | **Atualizar ficheiro** | Não | Não aplicável |
  ||||

* Os gatilhos SFTP-SSH não suportam o corte da mensagem. Ao solicitar o conteúdo do ficheiro, os gatilhos selecionam apenas ficheiros com 15 MB ou menores. Para obter ficheiros superiores a 15 MB, siga este padrão em vez disso:

  1. Utilize um gatilho SFTP-SSH que retorna apenas as propriedades de ficheiros, como **quando um ficheiro é adicionado ou modificado (apenas propriedades)** .

  1. Siga o gatilho com a ação de conteúdo de ficheiro SFTP-SSH **Get,** que lê o ficheiro completo e utiliza implicitamente a utilização de peças de texto.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Comparar SFTP-SSH contra SFTP

Aqui estão outras diferenças fundamentais entre o conector SFTP-SSH e o conector SFTP onde o conector SFTP-SSH tem estas capacidades:

* Utiliza a [biblioteca SSH.NET](https://github.com/sshnet/SSH.NET), que é uma biblioteca secure shell (SSH) de código aberto que suporta .NET.

* Fornece a ação **criar** pasta, que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação de **ficheiro Snome Rename,** que renomea um ficheiro no servidor SFTP.

* Caches a ligação ao servidor SFTP *por até 1 hora*, o que melhora o desempenho e reduz o número de tentativas de ligação ao servidor. Para definir a duração deste comportamento de cache, edite a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na configuração SSH no seu servidor SFTP.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O seu endereço de servidor SFTP e credenciais de conta, que permitem ao seu aplicativo lógico aceder à sua conta SFTP. Também precisa de acesso a uma chave privada SSH e à senha de chave privada SSH. Para utilizar o chunking ao carregar ficheiros grandes, precisa de ler e escrever permissões para a pasta raiz no seu servidor SFTP. Caso contrário, obtém-se um erro "401 Não Autorizado".

  > [!IMPORTANT]
  >
  > O conector SFTP-SSH suporta *apenas* estes formatos de chave privadas, algoritmos e impressões digitais:
  >
  > * **Formatos de chaves privadas**: RSA (Rivest Shamir Adleman) e DSA (Algoritmo de Assinatura Digital) em formatos OpenSSH e ssh.com. Se a sua chave privada estiver no formato de ficheiro PuTTY (.ppk), converta primeiro a chave para o formato de [ficheiro OpenSSH (.pem).](#convert-to-openssh)
  >
  > * **Algoritmos**de encriptação : DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  >
  > * **Impressão digital**: MD5
  >
  > Depois de adicionar o gatilho ou ação SFTP-SSH que deseja à sua aplicação lógica, tem de fornecer informações de ligação para o seu servidor SFTP. Quando fornecer a sua chave privada SSH para esta ligação, ***não introduza ou edite manualmente a tecla***, o que pode causar a falha da ligação. Em vez disso, certifique-se de copiar ***a chave*** do seu ficheiro de chave privada SSH e ***colar*** essa chave nos detalhes da ligação. 
  > Para mais informações, consulte o [Connect to SFTP com](#connect) a secção SSH mais tarde neste artigo.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SFTP. Para começar com um gatilho SFTP-SSH, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação SFTP-SSH, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="how-sftp-ssh-triggers-work"></a>Como o SFTP-SSH dispara

A SFTP-SSH desencadeia o trabalho através da sondagem do sistema de ficheiros SFTP e à procura de qualquer ficheiro que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem-lhe preservar a marca de tempo quando os ficheiros mudam. Nestes casos, tem de desativar esta funcionalidade para que o gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| Winscp | Ir a **Opções** > **Preferências** > **transferência** > **editar** > **preservar carimbo** de tempo > **Desativar** |
| FileZilla | Vá para **transferir** > **preservar selos de tempo de ficheiros transferidos** > **Desativar** |
|||

Quando um gatilho encontra um novo ficheiro, o gatilho verifica se o novo ficheiro está completo e não parcialmente escrito. Por exemplo, um ficheiro pode ter alterações em andamento quando o gatilho verificar o servidor de ficheiros. Para evitar a devolução de um ficheiro parcialmente escrito, o gatilho assinala a marca ção do ficheiro que tem alterações recentes, mas não devolve imediatamente esse ficheiro. O gatilho devolve o ficheiro apenas quando voltar a votar no servidor. Às vezes, este comportamento pode causar um atraso que é até o dobro do intervalo de votação do gatilho.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Converter a chave baseada em PuTTY para openSSH

Se a sua chave privada estiver no formato PuTTY, que utiliza a extensão do nome de ficheiro .ppk (PuTTY Private Key), converta primeiro a chave para o formato OpenSSH, que utiliza a extensão de nome de ficheiro .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Sistema operativo unix

1. Se as ferramentas PuTTY ainda não estiverem instaladas no seu sistema, faça isso agora, por exemplo:

   `sudo apt-get install -y putty`

1. Executar este comando, que cria um ficheiro que pode utilizar com o conector SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Por exemplo:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows OS

1. Se ainda não o fez, [descarregue a mais recente ferramenta PuTTY Generator (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)e, em seguida, lance a ferramenta.

1. Neste ecrã, selecione **Carregar**.

   ![Selecione "Carregar"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Navegue no seu ficheiro de chaves privadas em formato PuTTY e selecione **Open**.

1. No menu **Conversões,** selecione **tecla Export OpenSSH**.

   ![Selecione "Export OpenSSH key"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Guarde o ficheiro de chave privada com a extensão de nome de ficheiro `.pem`.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Ligue-se ao SFTP com sSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza `sftp ssh` como filtro. Na lista de gatilhos, selecione o gatilho que deseja.

   -ou-

   Para aplicações lógicas existentes, sob o último passo onde pretende adicionar uma ação, selecione **Novo passo**. Na caixa de pesquisa, introduza `sftp ssh` como filtro. Na lista de ações, selecione a ação que deseja.

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal plus **(+** ) que aparece e, em seguida, **selecione Adicionar uma ação**.

1. Forneça os detalhes necessários para a sua ligação.

   > [!IMPORTANT]
   >
   > Quando introduzir a sua chave privada SSH na propriedade **chave privada SSH,** siga estes passos adicionais, que ajudam a garantir que fornece o valor completo e correto para esta propriedade. Uma chave inválida faz com que a ligação falhe.

   Embora possa utilizar qualquer editor de texto, aqui estão os passos da amostra que mostram como copiar e colar corretamente a sua chave utilizando o Notepad.exe como exemplo.

   1. Abra o seu ficheiro de chave privada SSH num editor de texto. Estes passos utilizam o Bloco de Notas como exemplo.

   1. No menu **'Editar'** de notas, **selecione Selecione All**.

   1. **Selecione Editar** > **Copiar**.

   1. No gatilho ou ação SFTP-SSH adicionado, cola a chave *completa* que copiou na propriedade **chave privada SSH,** que suporta várias linhas.  ***Certifique-se de colar*** a chave. ***Não introduza manualmente ou edite a tecla***.

1. Quando terminar de introduzir os detalhes da ligação, selecione **Criar**.

1. Agora forneça os detalhes necessários para o seu gatilho ou ação selecionados e continue a construir o fluxo de trabalho da sua aplicação lógica.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Tamanho do pedaço de sobreposição

Para anular o comportamento adaptativo padrão que o chunking utiliza, pode especificar um tamanho constante de pedaço de 5 MB a 50 MB.

1. No canto superior direito da ação, selecione o botão elipses **(... )** e, em seguida, selecione **Definições**.

   ![Abrir definições SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. No âmbito **da Transferência de Conteúdos,** na propriedade do tamanho do **pedaço,** insira um valor inteiro de `5` a `50`, por exemplo: 

   ![Especificar o tamanho do pedaço para usar em vez disso](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Quando tiver terminado, selecione **Concluído**.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - Gatilho SSH: Quando um ficheiro é adicionado ou modificado

Este gatilho inicia um fluxo de trabalho de aplicação lógica quando um ficheiro é adicionado ou alterado num servidor SFTP. Por exemplo, pode adicionar uma condição que verifica o conteúdo do ficheiro e obtém o conteúdo com base no facto de o conteúdo cumprir uma condição especificada. Em seguida, pode adicionar uma ação que obtém o conteúdo do ficheiro e coloca esse conteúdo numa pasta no servidor SFTP.

**Exemplo da empresa:** Pode utilizar este gatilho para monitorizar uma pasta SFTP para novos ficheiros que representem as ordens do cliente. Em seguida, pode utilizar uma ação SFTP, como obter conteúdo de **ficheiros** para obter o conteúdo da encomenda para posterior processamento e guardar essa encomenda numa base de dados de encomendas.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-content-using-path"></a>SFTP - Ação SSH: Obtenha conteúdo usando caminho

Esta ação obtém o conteúdo de um ficheiro num servidor SFTP. Assim, por exemplo, pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do ficheiro deve satisfazer. Se a condição for verdadeira, a ação que obtém o conteúdo pode ser executada.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](https://docs.microsoft.com/connectors/sftpwithssh/).

> [!NOTE]
> Para aplicações lógicas num ambiente de serviço de [integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão do conector com o rótulo ISE utiliza os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
