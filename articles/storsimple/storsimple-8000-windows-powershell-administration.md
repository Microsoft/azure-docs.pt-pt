---
title: PowerShell para gestão de dispositivos do StorSimple | Documentos da Microsoft
description: Saiba como utilizar o Windows PowerShell para StorSimple para gerir o dispositivo StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli@microsoft.com
ms.openlocfilehash: e6053ef9b5e1fc113e3bd3057d627347f285cd99
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003132"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utilizar o Windows PowerShell para StorSimple para administrar o seu dispositivo

## <a name="overview"></a>Descrição geral

Windows PowerShell para StorSimple fornece uma interface de linha de comandos que pode utilizar para gerir o seu dispositivo do Microsoft Azure StorSimple. Como o nome sugere, é uma interface de linha de comandos baseada no Windows PowerShell, que é criada num espaço de execução restrito. Da perspectiva do usuário na linha de comandos, um espaço de execução restrito aparece como uma versão restrita do Windows PowerShell. Mantendo a alguns recursos básicos do Windows PowerShell, essa interface possui cmdlets dedicados adicionais que são direcionados para gerir o seu dispositivo do Microsoft Azure StorSimple.

Este artigo descreve o Windows PowerShell para funcionalidades do StorSimple, incluindo a forma como pode ligar a esta interface e contém hiperligações para procedimentos passo a passo ou fluxos de trabalho que pode realizar com esta interface. Os fluxos de trabalho incluem como registar o seu dispositivo, configure a interface de rede no seu dispositivo, instalar as atualizações que requerem o dispositivo estar no modo de manutenção, alterar o estado do dispositivo e resolver quaisquer problemas que ocorrem.

Depois de ler este artigo, será capaz de:

* Ligar ao dispositivo StorSimple com o Windows PowerShell para StorSimple.
* Administrar o seu dispositivo StorSimple com o Windows PowerShell para StorSimple.
* Obtenha ajuda no Windows PowerShell para StorSimple.

> [!NOTE]
> * Windows PowerShell para StorSimple cmdlets permitem-lhe gerir o dispositivo StorSimple a partir de uma consola de série ou remotamente através de comunicação remota do Windows PowerShell. Para obter mais informações sobre cada um dos cmdlets individuais que podem ser utilizados nessa interface, aceda a [referência de cmdlets para o Windows PowerShell para StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Os cmdlets do Azure PowerShell StorSimple são uma coleção diferente de cmdlets que permitem automatizar o nível de serviço do StorSimple e tarefas de migração da linha de comando. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para o [referência de cmdlets do Azure StorSimple](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Pode acessar o Windows PowerShell para StorSimple através de um dos seguintes métodos:

* [Ligar à consola de série de dispositivos do StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Ligar remotamente ao StorSimple com o Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Ligar para o Windows PowerShell para StorSimple através da consola de série do dispositivo

Pode [transfira o PuTTY](https://www.putty.org/) ou semelhante software de emulação do terminal para ligar ao Windows PowerShell para StorSimple. Tem de configurar o PuTTY especificamente para aceder ao dispositivo do Microsoft Azure StorSimple. Os tópicos seguintes contêm os passos detalhados sobre como configurar o PuTTy e ligar ao dispositivo. Várias opções de menu na consola de série do também são explicadas.

### <a name="putty-settings"></a>Definições do PuTTY

Certifique-se de que utilize as seguintes definições do PuTTY para ligar à interface do Windows PowerShell a partir da consola de série.

#### <a name="to-configure-putty"></a>Para configurar o PuTTY

1. No PuTTY **reconfiguração** caixa de diálogo a **categoria** painel, selecione **teclado**.
2. Certifique-se de que as seguintes opções estão selecionadas (são as configurações padrão quando inicia uma nova sessão).
   
   | Item de teclado | Selecione |
   | --- | --- |
   | Chave de retrocesso |Controle-? (127) |
   | Página inicial e final chaves |Standard |
   | Teclas de função e de teclado |ESC [n ~ |
   | Estado inicial de chaves de cursor |Normal |
   | Estado inicial do teclado numérico |Normal |
   | Ativar funcionalidades de teclado adicionais |Controle Alt é diferente do AltGr |
   
    ![Definições de Putty suportadas](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Clique em **Aplicar**.
4. Na **categoria** painel, selecione **tradução**.
5. Na **conjunto de carateres remotos** caixa de listagem, selecione **UTF-8**.
6. Sob **manipulação de caracteres de desenho de linha**, selecione **pontos de código de desenho de linha de utilização Unicode**. Captura de ecrã seguinte mostra as seleções PuTTY corretas.
   
    ![Definições do Putty UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Clique em **Aplicar**.

Agora, pode utilizar o PuTTY para ligar a consola de série do dispositivo, efetuando os seguintes passos.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Sobre a consola de série

Quando acessa o PowerShell do Windows é apresentada a interface do dispositivo StorSimple através da consola de série, uma mensagem de faixa, seguido de opções de menu.

A mensagem de faixa contém informações de dispositivo do StorSimple básicas, como o modelo, o nome, a versão do software instalado e o estado do controlador que está a aceder. A imagem seguinte mostra um exemplo de uma mensagem de faixa.

![Mensagem de faixa Serial](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Pode usar a mensagem de faixa para identificar se é o controlador está ligado ao _Active Directory_ ou _passivo_.

A imagem seguinte mostra as várias opções de espaço de execução que estão disponíveis no menu da consola de série.

![Registar o seu dispositivo 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Pode escolher as seguintes definições:

1. **Inicie sessão com acesso total** esta opção permite-lhe ligar (com as credenciais adequadas) para o **SSAdminConsole** espaço de execução no controlador de local. (O controlador local é o controlador que atualmente estão a aceder através da consola de série do dispositivo StorSimple). Esta opção também pode ser utilizada para permitir Support da Microsoft para aceder a sem restrições espaço de execução (uma sessão de suporte) para resolver quaisquer problemas de dispositivo possível. Depois de utilizar a opção 1 para iniciar sessão, pode permitir que o engenheiro de Support da Microsoft aceder ao espaço de execução sem restrições ao executar um cmdlet específico. Para obter detalhes, consulte [inicie uma sessão de suporte](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Inicie sessão no controlador de ponto a ponto com acesso total** esta opção é o mesmo que a opção 1, exceto que pode ligar (com as credenciais adequadas) para o **SSAdminConsole** espaço de execução no controlador de ponto a ponto. Uma vez que o dispositivo StorSimple é um dispositivo de elevada disponibilidade com dois controladores de uma configuração ativa-passiva, ponto a ponto refere-se para os demais controladores de dispositivo que está a aceder através da consola de série).
   Assim como a opção 1, esta opção pode também ser utilizada para permitir Support da Microsoft para aceder a sem restrições de espaço de execução num controlador de ponto a ponto.

3. **Ligar com acesso limitado** esta opção é utilizada para aceder à interface do Windows PowerShell no modo limitado. Não lhe é pedidas as credenciais de acesso. Esta opção se conecta a um espaço de execução mais restrito em comparação comparado as opções de 1 e 2.  Algumas das tarefas que estão disponíveis por meio da opção 1 que **não é possível* ser efetuada neste espaço de execução são:
   
   * Repor as definições de fábrica
   * Alterar a palavra-passe
   * Ativar ou desativar o acesso ao suporte
   * Aplicar atualizações
   * Instalar correções

     > [!NOTE]
     > Esta é a opção preferencial, se esqueceu a palavra-passe de administrador do dispositivo e não é possível ligar através da opção 1 ou 2.

4. **Alterar idioma** esta opção permite-lhe alterar o idioma de apresentação na interface do Windows PowerShell. Os idiomas suportados são o inglês, japonês, russo, francês, Coreia do Sul, espanhol, italiano, alemão, chinês e português (Brasil).

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Ligar remotamente ao StorSimple com o Windows PowerShell para StorSimple

Pode utilizar a comunicação remota do Windows PowerShell para ligar ao dispositivo StorSimple. Quando liga desta forma, não verá um menu. (Vê um menu apenas se utilizar a consola de série do dispositivo para ligar. Ligar remotamente leva diretamente para o equivalente a "opção 1 – acesso total" na consola de série.) Com a comunicação remota do Windows PowerShell, ligue para um espaço de execução específico. Também pode especificar o idioma de apresentação.

O idioma de apresentação é independente do idioma que definiu utilizando o **alterar idioma** opção no menu da consola de série. PowerShell remoto selecionará automaticamente a Localidade do dispositivo que está a ligar de se não for especificado nenhum.

> [!NOTE]
> Se estiver a trabalhar com hosts virtuais do Microsoft Azure e o StorSimple Cloud Appliances, pode utilizar a comunicação remota do Windows PowerShell e o anfitrião virtual para ligar à aplicação da cloud. Se tiver definido uma localização de partilha no anfitrião onde pretende guardar as informações da sessão do Windows PowerShell, deve estar ciente que a _todas as pessoas_ principal inclui somente usuários autenticados. Por conseguinte, se tiver configurado a partilha para permitir o acesso ao _todas as pessoas_ e ligar-se sem especificar credenciais, será utilizado o principal de anônimo não autenticado e verá um erro. Para corrigir este problema, no compartilhamento de anfitrião tem de ativar a conta de convidado e, em seguida, dar o convidado acesso total de conta para a partilha ou tem de especificar credenciais válidas, juntamente com o cmdlet do Windows PowerShell.


Pode utilizar HTTP ou HTTPS para ligar através de comunicação remota do Windows PowerShell. Utilize as instruções nos tutoriais seguintes:

* [Ligar remotamente através de HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Ligar remotamente através de HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considerações de segurança de ligação

Quando estiver a decidir como ligar ao Windows PowerShell para StorSimple, considere o seguinte:

* Ligar diretamente a consola de série do dispositivo é seguro, mas é ligar-se à consola de série através de comutadores de rede não. Tenha cuidado o risco de segurança ao ligar ao dispositivo serial através de comutadores de rede.
* Ligar através de uma sessão HTTP pode oferecer mais segurança do que se conectam através da consola de série sobre rede. Embora não seja o método mais seguro, é aceitável em redes fidedignas.
* Ligar através de uma sessão HTTPS é a mais segura e a opção recomendada.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrar o seu dispositivo StorSimple com o Windows PowerShell para StorSimple

A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados dentro da interface do Windows PowerShell do dispositivo StorSimple. Para obter mais informações sobre cada fluxo de trabalho, clique na entrada adequada na tabela.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell para StorSimple fluxos de trabalho

| Se quiser fazer isso... | Utilize este procedimento. |
| --- | --- |
| Registar o seu dispositivo |[Configurar e registar o dispositivo através do Windows PowerShell para StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Configurar proxy Web</br>Ver definições de proxy da web |[Configurar o proxy web para o dispositivo StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modificar as definições de interface de rede de 0 dados no seu dispositivo |[Modificar a interface rede DATA 0 para o dispositivo StorSimple](storsimple-8000-modify-data-0.md) |
| Parar um controlador </br> Reiniciar ou encerrar um controlador </br> Encerrar um dispositivo</br>Repor o dispositivo para as predefinições de fábrica |[Gerir controladores de dispositivo](storsimple-8000-manage-device-controller.md) |
| Instale os hotfixes e atualizações de modo de manutenção |[Atualizar o seu dispositivo](storsimple-update-device.md) |
| Introduza o modo de manutenção </br>Sair do modo de manutenção |[Modos de dispositivo do StorSimple](storsimple-8000-device-modes.md) |
| Criar um pacote de suporte</br>Desencriptar e editar um pacote de suporte |[Criar e gerir um pacote de suporte](storsimple-8000-create-manage-support-package.md) |
| Iniciar uma sessão de suporte</br> |[Iniciar uma sessão de suporte no Windows PowerShell para StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obtenha ajuda no Windows PowerShell para StorSimple

No Windows PowerShell para StorSimple, a ajuda do cmdlet está disponível. Uma versão online e atualizada dessa ajuda também está disponível, que pode ser usado para atualizar a ajuda no seu sistema.

Obter ajuda nessa interface é semelhante do Windows PowerShell e a maioria dos cmdlets relacionados com a ajuda funcionará. Pode encontrar ajuda para o Windows PowerShell online na Biblioteca TechNet: [Scripting com o Windows PowerShell](https://go.microsoft.com/fwlink/?LinkID=108518).

Segue-se uma breve descrição dos tipos de ajuda para esta interface do Windows PowerShell, incluindo como atualizar a ajuda.

### <a name="to-get-help-for-a-cmdlet"></a>Para obter ajuda para um cmdlet

* Para obter ajuda para qualquer cmdlet ou uma função, utilize o seguinte comando: `Get-Help <cmdlet-name>`
* Para obter ajuda online para qualquer cmdlet, utilize o cmdlet anterior com o `-Online` parâmetro: `Get-Help <cmdlet-name> -Online`
* Para obter ajuda completa, pode utilizar o `–Full` parâmetro e para obter exemplos, utilize o `–Examples` parâmetro.

### <a name="to-update-help"></a>Para atualizar a ajuda

Pode atualizar facilmente a ajuda na interface do Windows PowerShell. Execute os seguintes passos para atualizar a ajuda no seu sistema.

#### <a name="to-update-cmdlet-help"></a>Para atualizar a ajuda do cmdlet
1. Inicie o Windows PowerShell com o **executar como administrador** opção.
2. Na linha de comandos, escreva:  `Update-Help`
3. Os arquivos de ajuda atualizados serão instalados.
4. Depois de instalar os arquivos da ajuda, escreva: `Get-Help Get-Command`. Isso exibirá uma lista de cmdlets para o qual a ajuda está disponível.

> [!NOTE]
> Para obter uma lista de todos os cmdlets disponíveis num espaço de execução, inicie sessão para a opção de menu correspondente e execute o `Get-Command` cmdlet.


## <a name="next-steps"></a>Passos Seguintes

Se encontrar algum problema com o seu dispositivo StorSimple ao efetuar um dos fluxos de trabalho acima, consulte [ferramentas para resolver problemas de implementações do StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

