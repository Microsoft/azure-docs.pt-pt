---
title: Conecte-se ao servidor SFTP com o SSH
description: Automatizar tarefas que monitorizem, criem, gerem, enviem e recebam ficheiros para um servidor SFTP utilizando apps SSH e Azure Logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
tags: connectors
ms.openlocfilehash: cb851734dc8f71347168e7ac16ac0752845dda7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91823620"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>Monitorizar, criar e gerir ficheiros SFTP com o SSH e o Azure Logic Apps

Para automatizar tarefas que monitorizem, criem, enviem e recebam ficheiros num servidor [de Protocolo de Transferência de Ficheiros Seguro (SFTP),](https://www.ssh.com/ssh/sftp/) utilizando o protocolo Secure Shell [(SSH),](https://www.ssh.com/ssh/protocol/) pode construir e automatizar fluxos de trabalho de integração utilizando apps Azure Logic e o conector SFTP-SSH. O SFTP é um protocolo de rede que fornece acesso a ficheiros, transferência de ficheiros e gestão de ficheiros através de qualquer fluxo de dados fiável.

> [!NOTE]
> O conector SFTP-SSH não suporta atualmente estes servidores SFTP:
> 
> * IBM DataPower
> * Mensagem Caminho
> * OpenText Secure MFT
> * OpenText GXS

Aqui estão algumas tarefas de exemplo que pode automatizar:

* Monitorize quando os ficheiros forem adicionados ou alterados.
* Obtenha, crie, copie, rebatize, atualize, liste e elimine ficheiros.
* Criar pastas.
* Obtenha conteúdo de ficheiro e metadados.
* Extrair arquivos para pastas.

Pode utilizar gatilhos que monitorizem eventos no seu servidor SFTP e disponibilizem a saída para outras ações. Pode utilizar ações que executam várias tarefas no seu servidor SFTP. Também pode ter outras ações na sua aplicação lógica que utilizam a saída das ações da SFTP. Por exemplo, se recuperar regularmente ficheiros do seu servidor SFTP, pode enviar alertas de e-mail sobre esses ficheiros e o seu conteúdo utilizando o conector Do Office 365 Outlook ou o conector Outlook.com. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Para obter diferenças entre o conector SFTP-SSH e o conector SFTP, reveja a secção [Compare SFTP-SSH versus SFTP](#comparison) mais tarde neste tópico.

## <a name="limits"></a>Limites

* As ações SFTP-SSH que [suportam a chunking](../logic-apps/logic-apps-handle-large-messages.md) podem lidar com ficheiros até 1 GB, enquanto as ações SFTP-SSH que não suportam o chunking podem lidar com ficheiros até 50 MB. Embora o tamanho do pedaço padrão seja de 15 MB, este tamanho pode mudar dinamicamente, começando de 5 MB e aumentando gradualmente para o máximo de 50 MB, com base em fatores como latência de rede, tempo de resposta do servidor, e assim por diante.

  > [!NOTE]
  > Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector requer que se utilizem os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  Pode anular este comportamento adaptativo quando [especificar um tamanho constante](#change-chunk-size) de pedaço para usar. Este tamanho pode variar de 5 MB a 50 MB. Por exemplo, suponha que tem um ficheiro de 45 MB e uma rede que pode suportar esse tamanho de ficheiro sem latência. O chunking adaptativo resulta em várias chamadas, em vez daquela chamada. Para reduzir o número de chamadas, pode tentar definir um tamanho de pedaço de 50 MB. Em diferentes cenários, se a sua aplicação lógica estiver a cronometrar, por exemplo, ao utilizar pedaços de 15 MB, pode tentar reduzir o tamanho para 5 MB.

  O tamanho do pedaço está associado a uma ligação, o que significa que você pode usar a mesma conexão para ações que suportam o chunking e, em seguida, para ações que não suportam o chunking. Neste caso, o tamanho do pedaço para ações que não suportam o chunking varia de 5 MB a 50 MB. Este quadro mostra quais as ações da SFTP-SSH que suportam a chunking:

  | Ação | Suporte de chunking | Sobrepor suporte do tamanho do pedaço |
  |--------|------------------|-----------------------------|
  | **Ficheiro de cópia** | Não | Não aplicável |
  | **Criar ficheiro** | Sim | Sim |
  | **Criar pasta** | Não aplicável | Não aplicável |
  | **Eliminar mosaico** | Não aplicável | Não aplicável |
  | **Extrair arquivo para pasta** | Não aplicável | Não aplicável |
  | **Obtenha o conteúdo do arquivo** | Sim | Sim |
  | **Obtenha o conteúdo do ficheiro usando o caminho** | Sim | Sim |
  | **Obtenha metadados de ficheiros** | Não aplicável | Não aplicável |
  | **Obtenha metadados de ficheiros usando o caminho** | Não aplicável | Não aplicável |
  | **Listar ficheiros na pasta** | Não aplicável | Não aplicável |
  | **Arquivo de renomeação** | Não aplicável | Não aplicável |
  | **Atualizar ficheiro** | Não | Não aplicável |
  ||||

* Os gatilhos SFTP-SSH não suportam a mensagem a bater. Ao solicitar o conteúdo do ficheiro, os gatilhos selecionam apenas ficheiros com 15 MB ou menores. Para obter ficheiros maiores que 15 MB, siga este padrão em vez disso:

  1. Utilize um gatilho SFTP-SSH que retorna apenas propriedades de ficheiros, tais como **Quando um ficheiro é adicionado ou modificado (apenas propriedades)**.

  1. Siga o gatilho com a ação de conteúdo de **ficheiro** SFTP-SSH, que lê o ficheiro completo e utiliza implicitamente o corte de mensagens.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Compare SFTP-SSH versus SFTP

Aqui estão outras diferenças fundamentais entre o conector SFTP-SSH e o conector SFTP onde o conector SFTP-SSH tem estas capacidades:

* Utiliza a [biblioteca SSH.NET](https://github.com/sshnet/SSH.NET), que é uma biblioteca secure shell (SSH) de código aberto que suporta .NET.

* Fornece a ação **de pasta Create,** que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação **de ficheiro Rename,** que renomeia um ficheiro no servidor SFTP.

* Caches a ligação ao servidor SFTP *até 1 hora*, o que melhora o desempenho e reduz o número de tentativas de ligação ao servidor. Para definir a duração deste comportamento de caching, edite a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na configuração SSH no seu servidor SFTP.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O endereço do servidor SFTP e as credenciais de conta, que permitem que a sua aplicação lógica aceda à sua conta SFTP. Você também precisa de acesso a uma chave privada SSH e a senha de chave privada SSH. Para utilizar o chunking ao carregar ficheiros grandes, precisa de ler e escrever permissões para a pasta raiz no seu servidor SFTP. Caso contrário, obtém-se um erro "401 Não Autorizado".

  > [!IMPORTANT]
  >
  > O conector SFTP-SSH suporta *apenas* estes formatos de chave privadas, algoritmos e impressões digitais:
  >
  > * **Formatos chave privados**: Teclas RSA (Rivest Shamir Adleman) e DSA (Algoritmo de Assinatura Digital) em ambos os formatos OpenSSH e ssh.com. Se a sua chave privada estiver no formato de ficheiro PuTTY (.ppk), [primeiro converta a chave para o formato de ficheiro OpenSSH (.pem).](#convert-to-openssh)
  >
  > * **Algoritmos de encriptação**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  >
  > * **Impressão digital**: MD5
  >
  > Depois de adicionar o gatilho SFTP-SSH ou a ação que pretende à sua aplicação lógica, tem de fornecer informações de ligação para o seu servidor SFTP. Quando fornecer a sua chave privada SSH para esta ligação, ***não introduza ou edite manualmente a tecla***, o que pode causar a falha da ligação. Em vez disso, certifique-se de que ***copia a chave*** do seu ficheiro de chave privada SSH e ***cole*** essa chave nos detalhes da ligação. 
  > Para mais informações, consulte a [secção Connect to SFTP com SSH](#connect) mais tarde este artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder à sua conta SFTP. Para começar com um gatilho SFTP-SSH, [crie uma aplicação lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação SFTP-SSH, inicie a sua aplicação lógica com outro gatilho, por exemplo, o gatilho **recorrência.**

## <a name="how-sftp-ssh-triggers-work"></a>Como os acionadores do SFTP-SSH funcionam

A SFTP-SSH desencadeia o trabalho através da sondagem do sistema de ficheiros SFTP e à procura de qualquer ficheiro que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o tempo de tempo quando os ficheiros mudam. Nestes casos, tem de desativar esta funcionalidade para que o seu gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| Winscp | Ir a **Opções**  >  **Preferências**  >  **Transfer**  >  **Transferir Editar**  >  **Preservar horários Desativar**  >  **Disable** |
| FileZilla | Ir para **transferir**  >  **limites tempos de ficheiros transferidos Desativar**  >  **Disable** |
|||

Quando um gatilho encontra um novo ficheiro, o gatilho verifica se o novo ficheiro está completo e não parcialmente escrito. Por exemplo, um ficheiro pode ter alterações em andamento quando o gatilho verifica o servidor de ficheiros. Para evitar a devolução de um ficheiro parcialmente escrito, o gatilho anota o tempo de tempo para o ficheiro que tem alterações recentes, mas não devolve imediatamente esse ficheiro. O gatilho devolve o ficheiro apenas quando volta a sondar o servidor. Às vezes, este comportamento pode causar um atraso que é até o dobro do intervalo de votação do gatilho.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Converter a chave baseada em PuTTY para o OpenSSH

Se a sua chave privada estiver no formato PuTTY, que utiliza a extensão do nome do ficheiro .ppk (Chave Privada PuTTY), primeiro converta a chave para o formato OpenSSH, que utiliza a extensão do nome do ficheiro .pem (Privacy Enhanced Mail).

### <a name="unix-based-os"></a>Sistema operativo unix

1. Se as ferramentas PuTTY ainda não estiverem instaladas no seu sistema, faça-o agora, por exemplo:

   `sudo apt-get install -y putty`

1. Executar este comando, que cria um ficheiro que pode utilizar com o conector SFTP-SSH:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Por exemplo:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>SO Windows

1. Se ainda não o fez, [descarregue a mais recente ferramenta Do Gerador PuTTY (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)e, em seguida, lance a ferramenta.

1. Neste ecrã, selecione **Load**.

   ![Selecione "Carregar"](./media/connectors-sftp-ssh/puttygen-load.png)

1. Navegue no seu ficheiro chave privado no formato PuTTY e selecione **Open**.

1. No menu **Conversões,** selecione **Export OpenSSH .**

   ![Selecione "Export OpenSSH key"](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Guarde o ficheiro de chave privada com a extensão do nome do `.pem` ficheiro.

## <a name="considerations"></a>Considerações

Esta secção descreve considerações para rever os gatilhos e ações deste conector.

<a name="create-file"></a>

### <a name="create-file"></a>Criar ficheiro

Para criar um ficheiro no seu servidor SFTP, pode utilizar a ação de ficheiro SFTP-SSH **Create.** Quando esta ação cria o ficheiro, o serviço De aplicações lógicas também liga automaticamente para o seu servidor SFTP para obter os metadados do ficheiro. No entanto, se mover o ficheiro recém-criado antes que o serviço De Aplicações Lógicas possa fazer a chamada para obter os metadados, obtém uma `404` mensagem de erro, `'A reference was made to a file or folder which does not exist'` . Para não ler os metadados do ficheiro após a criação do ficheiro, siga os passos para [adicionar e definir a propriedade de **metadados de ficheiros para** **O**](#file-does-not-exist).

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Conecte-se ao SFTP com o SSH

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Para aplicações lógicas em branco, na caixa de pesquisa, introduza `sftp ssh` como filtro. Na lista de gatilhos, selecione o gatilho que pretende.

   -ou-

   Para aplicações lógicas existentes, sob o último passo onde pretende adicionar uma ação, selecione **Novo passo**. Na caixa de pesquisa, introduza `sftp ssh` como filtro. Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal de mais **+** () que aparece e, em seguida, selecione **Adicione uma ação**.

1. Forneça os detalhes necessários para a sua ligação.

   > [!IMPORTANT]
   >
   > Quando introduzir a sua chave privada SSH na propriedade **chave privada SSH,** siga estes passos adicionais, que ajudam a garantir que fornece o valor completo e correto para esta propriedade. Uma chave inválida faz com que a ligação falhe.

   Embora possa utilizar qualquer editor de texto, aqui estão os passos de amostra que mostram como copiar e colar corretamente a sua chave usando Notepad.exe como exemplo.

   1. Abra o seu ficheiro de chave privada SSH num editor de texto. Estes passos usam o Bloco de Notas como exemplo.

   1. No menu **Notepad Edit,** **selecione Select All**.

   1. Selecione **Edit**  >  **Editar Copy**.

   1. No gatilho ou ação SFTP-SSH que adicionou, cole a chave *completa* que copiou para a propriedade **chave privada SSH,** que suporta várias linhas.  ***Certifique-se de colar*** a chave. ***Não introduza ou edite manualmente a chave.***

1. Quando terminar de introduzir os detalhes da ligação, selecione **Criar**.

1. Agora forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Sobrepor o tamanho do pedaço

Para anular o comportamento adaptativo predefinido que o chunking utiliza, pode especificar um tamanho de pedaço constante de 5 MB a 50 MB.

1. No canto superior direito da ação, selecione o botão elipses **(... )** e, em seguida, selecione **Definições**.

   ![Abra as definições SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Em **Transferência de Conteúdo,** na propriedade **tamanho Chunk,** insira um valor inteiro `5` `50` de, por exemplo: 

   ![Especifique o tamanho do pedaço para usar em vez](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Quando tiver terminado, selecione **Concluído**.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - Gatilho SSH: Quando um ficheiro é adicionado ou modificado

Este gatilho inicia um fluxo de trabalho de aplicações lógicas quando um ficheiro é adicionado ou alterado num servidor SFTP. Por exemplo, pode adicionar uma condição que verifica o conteúdo do ficheiro e obtém o conteúdo com base no facto de o conteúdo cumprir uma condição especificada. Em seguida, pode adicionar uma ação que obtém o conteúdo do ficheiro e coloca esse conteúdo numa pasta no servidor SFTP.

**Exemplo da empresa**: Pode utilizar este gatilho para monitorizar uma pasta SFTP para novos ficheiros que representem as ordens dos clientes. Em seguida, pode utilizar uma ação SFTP, como **obter conteúdo de ficheiros,** para obter o conteúdo da encomenda para posterior processamento e armazenar essa encomenda numa base de dados de encomendas.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - Ação SSH: Obtenha conteúdo de ficheiros usando o caminho

Esta ação obtém o conteúdo de um ficheiro num servidor SFTP especificando o caminho do ficheiro. Assim, por exemplo, pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do ficheiro deve cumprir. Se a condição for verdadeira, a ação que obtém o conteúdo pode ser executada.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>Resolver erros

Esta secção descreve possíveis soluções para erros ou problemas comuns.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 erro: "Foi feita uma referência a um ficheiro ou pasta que não existe"

Este erro pode ocorrer quando a sua aplicação lógica cria um novo ficheiro no seu servidor SFTP através da ação de ficheiro SFTP-SSH **Create,** mas o ficheiro recém-criado é imediatamente movido antes que o serviço De aplicações lógicas possa obter os metadados do ficheiro. Quando a sua aplicação lógica executa a ação **de ficheiro Create,** o serviço De aplicações lógicas também liga automaticamente para o seu servidor SFTP para obter os metadados do ficheiro. No entanto, se o ficheiro for movido, o serviço De Aplicações Lógicas já não pode encontrar o ficheiro para obter a `404` mensagem de erro.

Se não conseguir evitar ou atrasar a deslocação do ficheiro, pode ignorar a leitura dos metadados do ficheiro após a criação do ficheiro, seguindo estes passos:

1. Na ação de **ficheiro Create,** abra a nova lista **de parâmetros Adicionar,** selecione a propriedade **'Obter todos os metadados de ficheiros'** e desabrocha o valor para **Nº**.

1. Se precisar deste ficheiro metadados mais tarde, pode utilizar a ação **de metadados de ficheiros Get.**

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 erro: "Uma tentativa de ligação falhou porque a parte ligada não respondeu corretamente após um período de tempo, ou a ligação estabelecida falhou porque o hospedeiro ligado não respondeu" ou "O pedido ao servidor SFTP demorou mais de '00:00:30' segundos"

Este erro pode ocorrer quando a aplicação lógica não é capaz de estabelecer com sucesso uma ligação com o servidor SFTP. Pode haver uma série de razões diferentes e sugerimos que se resolva a questão dos seguintes aspetos. 

1. O tempo de ligação é de 20 segundos. Certifique-se de que o servidor SFTP tem um bom desempenho e que os dispositivos intermidiados, como firewall, não adicionam muita sobrecarga. 

2. Se houver uma firewall envolvida, certifique-se de que os endereços IP do **conector gerido** são adicionados à lista aprovada. Você pode encontrar estes endereços IP para a sua região de aplicações lógicas [**aqui**]https://docs.microsoft.com/azure/logic-apps/logic-apps-limits-and-config#multi-tenant-azure---outbound-ip-addresses)

3. Se isto for um problema intermitente, por favor teste a definição de repetição para ver se uma contagem de repetição mais alta do que o padrão 4 pode ajudar.

4. Verifique se o servidor SFTP coloca um limite no número de ligações de cada endereço IP. Em caso afirmativo, poderá ter de limitar o número de instâncias de aplicações lógicas simultâneas. 

5. Aumentar a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) para uma hora na configuração SSH no seu servidor SFTP para reduzir o custo do estabelecimento de ligação.

6. Pode verificar o registo do servidor SFTP para ver se o pedido da aplicação lógica chegou ao servidor SFTP. Também pode tirar alguns vestígios de rede na sua firewall e no seu servidor SFTP para aprofundar a questão da conectividade.

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/sftpwithssh/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector requer que se utilizem os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
