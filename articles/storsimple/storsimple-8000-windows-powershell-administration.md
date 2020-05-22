---
title: PowerShell para gestão de dispositivos StorSimple
description: Saiba como utilizar o Windows PowerShell para o StorSimple para gerir o seu dispositivo StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 35be2e0b7b5a24261cd8ee15b1964b953c1a3327
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747715"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utilizar o Windows PowerShell para StorSimple para administrar o seu dispositivo

## <a name="overview"></a>Descrição geral

O Windows PowerShell para o StorSimple fornece uma interface de linha de comando que pode utilizar para gerir o seu dispositivo Microsoft Azure StorSimple. Como o nome sugere, trata-se de uma interface de linha de comando baseada no Windows PowerShell que é construída num espaço de execução limitado. Do ponto de vista do utilizador na linha de comando, um espaço de execução constrangido aparece como uma versão restrita do Windows PowerShell. Mantendo algumas das capacidades básicas do Windows PowerShell, esta interface tem cmdlets dedicados adicionais que estão orientados para a gestão do seu dispositivo Microsoft Azure StorSimple.

Este artigo descreve o Windows PowerShell para funcionalidades StorSimple, incluindo como pode ligar-se a esta interface, e contém links para procedimentos passo a passo ou fluxos de trabalho que pode executar usando esta interface. Os fluxos de trabalho incluem como registar o seu dispositivo, configurar a interface de rede no seu dispositivo, instalar atualizações que exijam que o dispositivo esteja no modo de manutenção, alterar o estado do dispositivo e resolver problemas que possa experimentar.

Depois de ler este artigo, poderá:

* Ligue-se ao seu dispositivo StorSimple utilizando o Windows PowerShell para o StorSimple.
* Administrar o seu dispositivo StorSimple utilizando o Windows PowerShell para o StorSimple.
* Obtenha ajuda no Windows PowerShell para o StorSimple.

> [!NOTE]
> * O Windows PowerShell para storSimple cmdlets permite-lhe gerir o seu dispositivo StorSimple a partir de uma consola em série ou remotamente através do remo do Windows PowerShell. Para obter mais informações sobre cada um dos cmdlets individuais que podem ser utilizados nesta interface, vá para a [referência cmdlet para Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Os storlets Azure PowerShell Simple são uma coleção diferente de cmdlets que lhe permitem automatizar tarefas de nível de serviço Emigração StorSimple a partir da linha de comando. Para mais informações sobre os cmdlets Azure PowerShell para StorSimple, vá à [referência de cmdlet Azure StorSimple](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Pode aceder ao Windows PowerShell para StorSimple utilizando um dos seguintes métodos:

* [Ligar à consola série do dispositivo StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Ligue-se remotamente ao StorSimple utilizando o Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ligue-se ao Windows PowerShell para StorSimple através da consola em série do dispositivo

Pode [descarregar putty](https://www.putty.org/) ou software de emulação de terminal semelhante para ligar ao Windows PowerShell para StorSimple. É necessário configurar o PuTTY especificamente para aceder ao dispositivo Microsoft Azure StorSimple. Os seguintes tópicos contêm passos detalhados sobre como configurar o PuTTy e ligar-se ao dispositivo. Várias opções de menu na consola em série também são explicadas.

### <a name="putty-settings"></a>Definições do PuTTY

Certifique-se de que utiliza as seguintes definições putty para se ligar à interface Do Windows PowerShell a partir da consola em série.

#### <a name="to-configure-putty"></a>Para configurar putty

1. Na caixa de diálogo de **reconfiguração** PuTTY, no painel **de categoria,** selecione **Teclado**.
2. Certifique-se de que são selecionadas as seguintes opções (estas são as definições predefinidas quando iniciar uma nova sessão).
   
   | Artigo de teclado | Selecione |
   | --- | --- |
   | Chave do backspace |Controlo? (127) |
   | Chaves para casa e fim |Standard |
   | Teclas de função e teclado |ESC[n~ |
   | Estado inicial das teclas do cursor |Normal |
   | Estado inicial do teclado numérico |Normal |
   | Ativar funcionalidades extra de teclado |Control-Alt é diferente do AltGr |
   
    ![Definições de putty suportadas](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Clique em **Aplicar**.
4. No painel **de categorias,** selecione **Tradução**.
5. Na caixa de listas de caracteres **remotos,** selecione **UTF-8**.
6. No **manuseamento de caracteres de desenho de linha,** selecione Use pontos de código de desenho da **linha Unicode**. A imagem que se segue mostra as seleções putty corretas.
   
    ![Definições de putty UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Clique em **Aplicar**.

Agora pode utilizar o PuTTY para se ligar à consola em série do dispositivo, fazendo os seguintes passos.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Sobre a consola em série

Quando aceder à interface Windows PowerShell do seu dispositivo StorSimple através da consola em série, é apresentada uma mensagem de banner, seguida de opções de menu.

A mensagem de banner contém informações básicas do dispositivo StorSimple, tais como o modelo, nome, versão de software instalada e estado do controlador a que acede. A imagem que se segue mostra um exemplo de uma mensagem de banner.

![Mensagem de banner em série](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Pode utilizar a mensagem de banner para identificar se o controlador a que está ligado é _Ativo_ ou _Passivo_.

A imagem que se segue mostra as várias opções de espaço de execução que estão disponíveis no menu de consola seleções em série.

![Registe o seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Pode escolher entre as seguintes definições:

1. **Iniciar sessão com acesso completo** Esta opção permite-lhe ligar (com as credenciais adequadas) ao espaço de execução **SSAdminConsole** no controlador local. (O controlador local é o controlador a que está a aceder através da consola em série do seu dispositivo StorSimple.) Esta opção também pode ser usada para permitir que o Microsoft Support aceda ao espaço de execução sem restrições (uma sessão de suporte) para resolver qualquer problema com possíveis problemas de dispositivo. Depois de utilizar a opção 1 para iniciar sessão, pode permitir ao engenheiro de suporte do Microsoft aceder ao espaço de execução sem restrições executando um cmdlet específico. Para mais detalhes, consulte iniciar [uma sessão](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)de suporte .
   
2. **Inicie sessão no controlador de pares com acesso total** Esta opção é a mesma que a opção 1, exceto que pode ligar (com as credenciais adequadas) ao espaço de execução **SSAdminConsole** no controlador de pares. Uma vez que o dispositivo StorSimple é um dispositivo de alta disponibilidade com dois controladores numa configuração activa-passiva, o peer refere-se ao outro controlador no dispositivo a que está a aceder através da consola em série).
   Semelhante à opção 1, esta opção também pode ser usada para permitir que o Microsoft Support aceda a um espaço de execução sem restrições num controlador de pares.

3. **Conecte-se com acesso limitado** Esta opção é utilizada para aceder à interface Do Windows PowerShell em modo limitado. Não são solicitados para credenciais de acesso. Esta opção liga-se a um espaço de corrida mais restrito em comparação com as opções 1 e 2.  Algumas das tarefas que estão disponíveis através da opção 1 que **não podem* ser executadas neste espaço de execução são:
   
   * Reset para as definições de fábrica
   * Alterar a palavra-passe
   * Ativar ou desativar o acesso ao suporte
   * Aplicar atualizações
   * Instale hotfixes

     > [!NOTE]
     > Esta é a opção preferida se se ter esquecido da palavra-passe do administrador do dispositivo e não conseguir ligar através da opção 1 ou 2.

4. **Mudar de linguagem** Esta opção permite alterar o idioma do ecrã na interface Do Windows PowerShell. As línguas apoiadas são inglês, japonês, russo, francês, sul-coreano, espanhol, italiano, alemão, chinês e português.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Ligue-se remotamente ao StorSimple utilizando o Windows PowerShell para storSimple

Pode utilizar o remo do Windows PowerShell para se ligar ao seu dispositivo StorSimple. Quando ligar desta forma, não verá um menu. (Só se vê um menu se utilizar a consola em série do dispositivo para se ligar. Ligar remotamente leva-o diretamente ao equivalente à "opção 1 – acesso total" na consola em série.) Com o remo do Windows PowerShell, liga-se a um espaço de execução específico. Também pode especificar a linguagem de exibição.

A linguagem de exibição é independente do idioma que definiu utilizando a opção **Change Language** no menu de consola em série. O Remote PowerShell captará automaticamente o local do dispositivo de que está a ligar se nenhum for especificado.

> [!NOTE]
> Se estiver a trabalhar com os anfitriões virtuais do Microsoft Azure e com os Eletrodomésticos StorSimple Cloud, pode utilizar o remo do Windows PowerShell e o hospedeiro virtual para se ligar ao aparelho em nuvem. Se tiver configurado uma localização de partilha no anfitrião para guardar informações da sessão do Windows PowerShell, deve estar ciente de que o principal _de Todos_ inclui apenas utilizadores autenticados. Portanto, se tiver configurado a parte para permitir o acesso por _todos_ e se conectar sem especificar credenciais, o diretor anónimo não autenticado será utilizado e verá um erro. Para corrigir este problema, no anfitrião da partilha tem de ativar a conta De Convidado e, em seguida, dar à conta De Convidado acesso total à partilha ou deve especificar credenciais válidas juntamente com o cmdlet do Windows PowerShell.


Pode utilizar HTTP ou HTTPS para se ligar através do remo do Windows PowerShell. Utilize as instruções nos seguintes tutoriais:

* [Conecte-se remotamente usando HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Conecte-se remotamente usando HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de ligação

Quando estiver a decidir como ligar-se ao Windows PowerShell para o StorSimple, considere o seguinte:

* A ligação direta à consola em série do dispositivo é segura, mas ligar-se à consola em série sobre os interruptores de rede não é. Tenha cuidado com o risco de segurança ao ligar-se à série do dispositivo sobre os interruptores de rede.
* Ligar-se através de uma sessão HTTP pode oferecer mais segurança do que ligar através da consola em série através da rede. Embora este não seja o método mais seguro, é aceitável em redes fidedignas.
* Ligar através de uma sessão HTTPS é a opção mais segura e recomendada.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar o seu dispositivo StorSimple utilizando o Windows PowerShell para storSimple

A tabela seguinte mostra um resumo de todas as tarefas comuns de gestão e fluxos de trabalho complexos que podem ser realizados dentro da interface Windows PowerShell do seu dispositivo StorSimple. Para obter mais informações sobre cada fluxo de trabalho, clique na entrada adequada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell para fluxos de trabalho StorSimple

| Se quiser fazer isto... | Use este procedimento. |
| --- | --- |
| Registar o seu dispositivo |[Configure e registe o dispositivo utilizando o Windows PowerShell para o StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Configurar proxy Web</br>Ver definições de procuração web |[Configure procuração web para o seu dispositivo StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modificar as definições de interface de rede DATA 0 no seu dispositivo |[Modificar a interface de rede DATA 0 para o seu dispositivo StorSimple](storsimple-8000-modify-data-0.md) |
| Parar um controlador </br> Reiniciar ou desligar um controlador </br> Desligue um dispositivo</br>Reset the device to factory default settings (Repor o dispositivo para as predefinições de fábrica) |[Gerir controladores de dispositivos](storsimple-8000-manage-device-controller.md) |
| Instale atualizações e hotfixes do modo de manutenção |[Atualizar o seu dispositivo](storsimple-update-device.md) |
| Insira o modo de manutenção </br>Modo de manutenção de saída |[Modos de dispositivo StorSimple](storsimple-8000-device-modes.md) |
| Criar um pacote de suporte</br>Desencriptar e editar um pacote de suporte |[Criar e gerir um pacote de suporte](storsimple-8000-create-manage-support-package.md) |
| Iniciar uma sessão de apoio</br> |[Inicie uma sessão de suporte no Windows PowerShell para storSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obtenha ajuda no Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, a cmdlet Help está disponível. Também está disponível uma versão online atualizada desta Ajuda, que pode utilizar para atualizar a Ajuda no seu sistema.

Obter ajuda nesta interface é semelhante ao do Windows PowerShell, e a maioria dos cmdlets relacionados com a Ajuda funcionará. Pode encontrar ajuda para o Windows PowerShell online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Segue-se uma breve descrição dos tipos de Ajuda para esta interface Windows PowerShell, incluindo como atualizar a Ajuda.

### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

* Para obter ajuda para qualquer cmdlet ou função, use o seguinte comando:`Get-Help <cmdlet-name>`
* Para obter ajuda on-line para qualquer cmdlet, use o cmdlet anterior com o `-Online` parâmetro:`Get-Help <cmdlet-name> -Online`
* Para obter a ajuda completa, pode utilizar o `–Full` parâmetro e, por exemplo, utilizar o `–Examples` parâmetro.

### <a name="to-update-help"></a>Para atualizar a Ajuda

Pode atualizar facilmente a Ajuda na interface Windows PowerShell. Execute os seguintes passos para atualizar a Ajuda no seu sistema.

#### <a name="to-update-cmdlet-help"></a>Para atualizar a ajuda cmdlet
1. Inicie o Windows PowerShell com a executar como opção **de administrador.**
2. No pedido de comando, escreva:`Update-Help`
3. Os ficheiros de Ajuda atualizados serão instalados.
4. Depois de instalados os ficheiros ajuda, escreva: `Get-Help Get-Command` . Isto apresentará uma lista de cmdlets para os quais a Ajuda está disponível.

> [!NOTE]
> Para obter uma lista de todos os cmdlets disponíveis num espaço de execução, faça login na opção de menu correspondente e faça o `Get-Command` cmdlet.


## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema com o seu dispositivo StorSimple ao executar um dos fluxos de trabalho acima referidos, consulte as Ferramentas para resolução de problemas De [storSimple implementações](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

