---
title: Conecte-se ao servidor SFTP com o SSH
description: Automatizar tarefas que monitorizem, criem, gerem, enviem e recebam ficheiros para um servidor SFTP utilizando apps SSH e Azure Logic
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/19/2021
tags: connectors
ms.openlocfilehash: a19253e117f748b4d4045bfd2a29552018bba91e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781564"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>Criar e gerir ficheiros SFTP utilizando apps SSH e Azure Logic

Para automatizar tarefas que criam e gerem ficheiros num servidor [secure file transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) utilizando o protocolo Secure Shell [(SSH),](https://www.ssh.com/ssh/protocol/) pode criar fluxos de trabalho de integração automatizados utilizando apps lógicas Azure e o conector SFTP-SSH. O SFTP é um protocolo de rede que fornece acesso a ficheiros, transferência de ficheiros e gestão de ficheiros através de qualquer fluxo de dados fiável.

Aqui estão algumas tarefas de exemplo que pode automatizar:

* Monitorize quando os ficheiros forem adicionados ou alterados.
* Obtenha, crie, copie, rebatize, atualize, liste e elimine ficheiros.
* Criar pastas.
* Obtenha conteúdo de ficheiro e metadados.
* Extrair arquivos para pastas.

No seu fluxo de trabalho, pode utilizar um gatilho que monitoriza eventos no seu servidor SFTP e disponibiliza a saída para outras ações. Em seguida, pode utilizar ações para executar várias tarefas no seu servidor SFTP. Também pode incluir outras ações que utilizam a saída das ações SFTP-SSH. Por exemplo, se recuperar regularmente ficheiros do seu servidor SFTP, pode enviar alertas de e-mail sobre esses ficheiros e o seu conteúdo utilizando o conector do Office 365 Outlook ou o conector Outlook.com. Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Para obter diferenças entre o conector SFTP-SSH e o conector SFTP, reveja a secção [Compare SFTP-SSH versus SFTP](#comparison) mais tarde neste tópico.

## <a name="limits"></a>Limites

* O conector SFTP-SSH não suporta atualmente estes servidores SFTP:

  * IBM DataPower
  * Mensagem Caminho
  * OpenText Secure MFT
  * OpenText GXS

* As ações SFTP-SSH que [suportam a chunking](../logic-apps/logic-apps-handle-large-messages.md) podem lidar com ficheiros até 1 GB, enquanto as ações SFTP-SSH que não suportam o chunking podem lidar com ficheiros até 50 MB. O tamanho do pedaço predefinido é de 15 MB. No entanto, este tamanho pode mudar dinamicamente, a partir de 5 MB e gradualmente aumentando para o máximo de 50-MB. O dimensionamento dinâmico baseia-se em fatores como a latência da rede, o tempo de resposta do servidor, e assim por diante.

  > [!NOTE]
  > Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector requer que se utilizem os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  Pode anular este comportamento adaptativo quando [especificar um tamanho constante](#change-chunk-size) de pedaço para usar. Este tamanho pode variar de 5 MB a 50 MB. Por exemplo, suponha que tem um ficheiro de 45-MB e uma rede que pode suportar esse tamanho de ficheiro sem latência. O chunking adaptativo resulta em várias chamadas, em vez daquela chamada. Para reduzir o número de chamadas, pode tentar definir um tamanho de pedaço de 50 MB. Em diferentes cenários, se a sua aplicação lógica estiver a cronometrar, por exemplo, ao utilizar pedaços de 15-MB, pode tentar reduzir o tamanho para 5 MB.

  O tamanho do pedaço está associado a uma ligação. Este atributo significa que pode usar a mesma ligação para ambas as ações que suportam o chunking e ações que não suportam o chunking. Neste caso, o tamanho do pedaço para ações que não suportam o chunking varia de 5 MB a 50 MB. Este quadro mostra quais as ações da SFTP-SSH que suportam a chunking:

  | Ação | Suporte de chunking | Sobrepor suporte do tamanho do pedaço |
  |--------|------------------|-----------------------------|
  | **Ficheiro de cópia** | No | Não aplicável |
  | **Criar ficheiro** | Yes | Yes |
  | **Criar pasta** | Não aplicável | Não aplicável |
  | **Eliminar ficheiro** | Não aplicável | Não aplicável |
  | **Extrair arquivo para pasta** | Não aplicável | Não aplicável |
  | **Obtenha o conteúdo do arquivo** | Yes | Yes |
  | **Obtenha o conteúdo do ficheiro usando o caminho** | Yes | Yes |
  | **Obtenha metadados de ficheiros** | Não aplicável | Não aplicável |
  | **Obtenha metadados de ficheiros usando o caminho** | Não aplicável | Não aplicável |
  | **Listar ficheiros na pasta** | Não aplicável | Não aplicável |
  | **Arquivo de renomeação** | Não aplicável | Não aplicável |
  | **Atualizar ficheiro** | No | Não aplicável |
  ||||

* Os gatilhos SFTP-SSH não suportam a mensagem a bater. Ao solicitar o conteúdo do ficheiro, os gatilhos selecionam apenas ficheiros com 15 MB ou menores. Para obter ficheiros maiores que 15 MB, siga este padrão em vez disso:

  1. Utilize um gatilho SFTP-SSH que retorna apenas propriedades de ficheiros. Estes gatilhos têm nomes que incluem a descrição, **(apenas propriedades)**.

  1. Siga o gatilho com a ação de conteúdo de **ficheiro** SFTP-SSH. Esta ação lê o ficheiro completo e utiliza implicitamente o chunking de mensagens.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Compare SFTP-SSH versus SFTP

A lista a seguir descreve as principais capacidades SFTP-SSH que diferem do conector SFTP:

* Utiliza a [biblioteca SSH.NET](https://github.com/sshnet/SSH.NET), que é uma biblioteca secure shell (SSH) de código aberto que suporta .NET.

* Fornece a ação **de pasta Create,** que cria uma pasta no caminho especificado no servidor SFTP.

* Fornece a ação **de ficheiro Rename,** que renomeia um ficheiro no servidor SFTP.

* Caches a ligação ao servidor SFTP *por um máximo de 1 hora*. Esta capacidade melhora o desempenho e reduz a frequência com que o conector tenta ligar-se ao servidor. Para definir a duração deste comportamento de caching, edite a propriedade [ **ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) na configuração SSH no seu servidor SFTP.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* O endereço do servidor SFTP e as credenciais de conta, para que o seu fluxo de trabalho possa aceder à sua conta SFTP. Você também precisa de acesso a uma chave privada SSH e a senha de chave privada SSH. Para fazer o upload de ficheiros grandes utilizando o chunking, é necessário ler e escrever o acesso para a pasta raiz no seu servidor SFTP. Caso contrário, obtém-se um erro "401 Não Autorizado".

  O conector SFTP-SSH suporta a autenticação privada da chave e a autenticação por palavra-passe. No entanto, o conector SFTP-SSH suporta *apenas* estes formatos de chaves privadas, algoritmos e impressões digitais:

  * **Formatos chave privados**: Teclas RSA (Rivest Shamir Adleman) e DSA (Algoritmo de Assinatura Digital) em ambos os formatos OpenSSH e ssh.com. Se a sua chave privada estiver no formato de ficheiro PuTTY (.ppk), [primeiro converta a chave para o formato de ficheiro OpenSSH (.pem).](#convert-to-openssh)
  * **Algoritmos de encriptação**: DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC e AES-256-CBC
  * **Impressão digital**: MD5

  Depois de adicionar um gatilho SFTP-SSH ou ação ao seu fluxo de trabalho, tem de fornecer informações de ligação para o seu servidor SFTP. Quando fornecer a sua chave privada SSH para esta ligação, ***não introduza manualmente ou edite a tecla** _, o que pode causar a falha da ligação. Em vez disso, certifique-se de que _*_copia a chave_*_ do seu ficheiro de chave privada SSH e _ *_cole_** essa chave nos detalhes da ligação. Para mais informações, consulte a [secção Connect to SFTP com SSH](#connect) mais tarde este artigo.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O fluxo de trabalho de aplicações lógicas onde pretende aceder à sua conta SFTP. Para começar com um gatilho SFTP-SSH, [crie um fluxo de trabalho de aplicações lógicas em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para utilizar uma ação SFTP-SSH, inicie o seu fluxo de trabalho com outro gatilho, por exemplo, o gatilho **de Recorrência.**

## <a name="how-sftp-ssh-triggers-work"></a>Como os acionadores do SFTP-SSH funcionam

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Comportamento das sondagens

A SFTP-SSH desencadeia a sondagem do sistema de ficheiros SFTP e procura qualquer ficheiro que tenha mudado desde a última sondagem. Algumas ferramentas permitem preservar o tempo de tempo quando os ficheiros mudam. Nestes casos, tem de desativar esta funcionalidade para que o seu gatilho possa funcionar. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| Winscp | Ir a **Opções**  >  **Preferências**  >    >  **Transferir Editar**  >  **Preservar horários Desativar**  >   |
| FileZilla | Ir para **transferir**  >  **limites tempos de ficheiros transferidos Desativar**  >   |
|||

Quando um gatilho encontra um novo ficheiro, o gatilho verifica se o novo ficheiro está completo e não parcialmente escrito. Por exemplo, um ficheiro pode ter alterações em andamento quando o gatilho verifica o servidor de ficheiros. Para evitar a devolução de um ficheiro parcialmente escrito, o gatilho anota o tempo de tempo para o ficheiro que tem alterações recentes, mas não devolve imediatamente esse ficheiro. O gatilho devolve o ficheiro apenas quando volta a sondar o servidor. Às vezes, este comportamento pode causar um atraso que é até o dobro do intervalo de votação do gatilho.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Mudança de recorrência do gatilho e deriva

Os gatilhos baseados em ligação onde é necessário criar uma ligação em primeiro lugar, como o gatilho SFTP-SSH, diferem dos gatilhos incorporados que funcionam de forma nativa em Azure Logic Apps, como o [gatilho de Recorrência](../connectors/connectors-native-recurrence.md). Em gatilhos baseados em ligação recorrente, o calendário de recorrência não é o único condutor que controla a execução, e o fuso horário apenas determina a hora de início inicial. As execuções subsequentes dependem do calendário de recorrência, da última execução do *gatilho, e* de outros fatores que podem causar tempos de fuga ou produzir comportamentos inesperados. Por exemplo, o comportamento inesperado pode incluir a falha na manutenção do horário especificado quando o horário de verão (DST) começa e termina. Para garantir que o tempo de recorrência não muda quando o DST faz efeito, ajuste manualmente a recorrência. Assim, o seu fluxo de trabalho continua a funcionar no momento esperado. Caso contrário, a hora de início muda uma hora para a frente quando o DST começa e uma hora para trás quando o DST termina. Para obter mais informações, consulte [Recorrência para gatilhos baseados em ligação](../connectors/apis-list.md#recurrence-for-connection-based-triggers).

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Converter a chave baseada em PuTTY para o OpenSSH

O formato PuTTY e o formato OpenSSH utilizam diferentes extensões de nome de ficheiro. O formato PuTTY utiliza a extensão do nome do ficheiro .ppk, ou PuTTY Private. O formato OpenSSH utiliza a extensão de nome de ficheiro .pem, ou Privacy Enhanced Mail. Se a sua chave privada estiver no formato PuTTY e tiver de utilizar o formato OpenSSH, primeiro converta a chave para o formato OpenSSH seguindo estes passos:

### <a name="unix-based-os"></a>Sistema operativo unix

1. Se não tiver as ferramentas PuTTY instaladas no seu sistema, faça-o agora, por exemplo:

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

Esta secção descreve considerações a rever quando utiliza os gatilhos e ações deste conector.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Utilize diferentes pastas SFTP para o upload e processamento de ficheiros

No seu servidor SFTP, utilize pastas separadas para armazenar ficheiros carregados e para que o gatilho monitorize esses ficheiros para o processamento. Caso contrário, o gatilho não dispara e comporta-se de forma imprevisível, por exemplo, ignorando um número aleatório de ficheiros que o gatilho processa. No entanto, este requisito significa que precisa de uma forma de mover ficheiros entre essas pastas. 

Se este problema de gatilho acontecer, retire os ficheiros da pasta que o gatilho monitoriza e utilize uma pasta diferente para armazenar os ficheiros carregados.

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

   1. Selecione   >  **Editar Copy**.

   1. No gatilho ou ação SFTP-SSH, *cole a* chave copiada completa na propriedade chave **privada SSH,** que suporta várias linhas. **_Não introduza ou edite manualmente a chave._**

1. Depois de terminar de introduzir os detalhes da ligação, selecione **Criar**.

1. Agora forneça os detalhes necessários para o seu gatilho ou ação selecionado e continue a construir o fluxo de trabalho da sua aplicação lógica.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Sobrepor o tamanho do pedaço

Para anular o comportamento adaptativo predefinido que o chunking utiliza, pode especificar um tamanho de pedaço constante de 5 MB a 50 MB.

1. No canto superior direito da ação, selecione o botão elipses **(... )** e, em seguida, selecione **Definições**.

   ![Abra as definições SFTP-SSH](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. Em **Transferência de Conteúdo,** na propriedade **tamanho Chunk,** insira um valor inteiro `5` `50` de, por exemplo: 

   ![Especifique o tamanho do pedaço para usar em vez](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Depois de terminar, selecione **'Fazer'.**

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP - Gatilho SSH: Quando um ficheiro é adicionado ou modificado

Este gatilho inicia um fluxo de trabalho quando um ficheiro é adicionado ou alterado num servidor SFTP. Como exemplo de ações de acompanhamento, o fluxo de trabalho pode usar uma condição para verificar se o conteúdo do ficheiro cumpre os critérios especificados. Se o conteúdo satisfaça a condição, a ação SFTP-SSH do conteúdo do **ficheiro Get** SFTP-SSH pode obter o conteúdo e, em seguida, outra ação SFTP-SSH pode colocar esse ficheiro numa pasta diferente no servidor SFTP.

**Exemplo da empresa**: Pode utilizar este gatilho para monitorizar uma pasta SFTP para novos ficheiros que representem as ordens dos clientes. Em seguida, pode utilizar uma ação SFTP-SSH, como obter o conteúdo do **ficheiro,** para obter o conteúdo da encomenda para posterior processamento e armazenar essa encomenda numa base de dados de encomendas.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP - Ação SSH: Obtenha conteúdo de ficheiros usando o caminho

Esta ação obtém o conteúdo de um ficheiro num servidor SFTP especificando o caminho do ficheiro. Assim, por exemplo, pode adicionar o gatilho do exemplo anterior e uma condição que o conteúdo do ficheiro deve cumprir. Se a condição for verdadeira, a ação que obtém o conteúdo pode ser executada.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Resolução de problemas

Esta secção descreve possíveis soluções para erros ou problemas comuns.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 erro: "Uma tentativa de ligação falhou porque a parte ligada não respondeu corretamente após um período de tempo, ou a ligação estabelecida falhou porque o hospedeiro ligado não respondeu" ou "O pedido ao servidor SFTP demorou mais de '00:00:30' segundos"

Este erro pode ocorrer quando a sua aplicação lógica não consegue estabelecer uma ligação com o servidor SFTP com sucesso. Pode haver diferentes razões para este problema, por isso experimente estas opções de resolução de problemas:

* O tempo limite de ligação é de 20 segundos. Verifique se o seu servidor SFTP tem um bom desempenho e dispositivos intermédios, como firewalls, não estão a adicionar sobrecarga. 

* Se tiver uma firewall configurada, certifique-se de que adiciona os endereços **IP do conector gerido** à lista aprovada. Para encontrar os endereços IP para a região da sua aplicação lógica, consulte [Limites e configuração para Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses).

* Se este erro acontecer intermitentemente, altere a definição de **política de Retry** na ação SFTP-SSH para uma contagem de repetição superior às quatro recauchutagens predefinidos.

* Verifique se o servidor SFTP coloca um limite no número de ligações de cada endereço IP. Se existir um limite, poderá ter de limitar o número de instâncias de aplicações lógicas simultâneas.

* Para reduzir o custo do estabelecimento de ligação, na configuração SSH para o seu servidor SFTP, aumente a propriedade [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) para cerca de uma hora.

* Reveja o registo do servidor SFTP para verificar se o pedido da aplicação lógica chegou ao servidor SFTP. Para obter mais informações sobre o problema de conectividade, também pode executar um rastreio de rede na sua firewall e no seu servidor SFTP.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 erro: "Foi feita uma referência a um ficheiro ou pasta que não existe"

Este erro pode ocorrer quando o seu fluxo de trabalho cria um ficheiro no seu servidor SFTP com a ação de ficheiro SFTP-SSH **Create,** mas move imediatamente esse ficheiro antes que o serviço De aplicações lógicas possa obter os metadados do ficheiro. Quando o seu fluxo de trabalho executa a ação **de ficheiros Create,** o serviço De aplicações lógicas liga automaticamente para o servidor SFTP para obter os metadados do ficheiro. No entanto, se a sua aplicação lógica mover o ficheiro, o serviço De Aplicações Lógicas já não pode encontrar o ficheiro para obter a `404` mensagem de erro.

Se não conseguir evitar ou atrasar a deslocação do ficheiro, pode ignorar a leitura dos metadados do ficheiro após a criação do ficheiro, seguindo estes passos:

1. Na ação de **ficheiro Create,** abra a nova lista **de parâmetros Adicionar,** selecione a propriedade **'Obter todos os metadados de ficheiros'** e desabrocha o valor para **Nº**.

1. Se precisar deste ficheiro metadados mais tarde, pode utilizar a ação **de metadados de ficheiros Get.**

## <a name="connector-reference"></a>Referência do conector

Para obter mais detalhes técnicos sobre este conector, tais como gatilhos, ações e limites descritos pelo ficheiro Swagger do conector, consulte a [página de referência do conector](/connectors/sftpwithssh/).

> [!NOTE]
> Para aplicações lógicas num ambiente de [serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)a versão com rótulo ISE deste conector requer que se utilizem os limites de [mensagem ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)
