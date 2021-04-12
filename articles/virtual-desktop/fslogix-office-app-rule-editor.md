---
title: Instale os recipientes de aplicação do Microsoft Office FSLogix no Windows Virtual Desktop - Azure
description: Como utilizar o editor de regras da aplicação para criar um recipiente de aplicação FSLogix com o Office in Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a144adcfbf6c7cefc6b946f95bdb734868de801f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446813"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Instale o Microsoft Office utilizando recipientes de aplicação FSLogix

Pode instalar o Microsoft Office de forma rápida e eficiente utilizando um recipiente de aplicação FSLogix como modelo para as outras máquinas virtuais (VMs) na sua piscina de anfitrião.

Aqui está a razão pela qual usar um recipiente de aplicações FSLogix pode ajudar a tornar a instalação mais rápida:

- Descarregar as suas aplicações office para um recipiente de aplicações reduz os requisitos para o seu tamanho de unidade C.
- As fotos ou cópias de segurança do seu VM requerem menos recursos.
- Ter um pipeline automatizado através da atualização de uma única imagem facilita a atualização dos seus VMs.
- Só precisa de uma imagem para instalar o Office (e outras aplicações) em todos os VMs na sua implementação virtual do Windows Desktop.

Este artigo irá mostrar-lhe como configurar um recipiente de aplicação FSLogix com o Office.

## <a name="requirements"></a>Requisitos

Vai precisar das seguintes coisas para configurar o editor de regras:

- um VM executando janelas sem Office instalado
- uma cópia do Office
- uma cópia do FSLogix instalada na sua implantação
- uma partilha de rede que todos os VMs na sua piscina de anfitrião têm acesso apenas de leitura a

## <a name="install-office"></a>Instalação de Escritório

Para instalar o Office no seu VHD ou VHDX, ative o Protocolo de Ambiente de Trabalho Remoto no seu VM e, em seguida, siga as instruções [no Gabinete de Instalação numa imagem principal VHD](install-office-on-wvd-master-image.md). Ao instalar, certifique-se de que utiliza [as licenças corretas](overview.md#requirements).

>[!NOTE]
>O Windows Virtual Desktop requer ativação de computador de partilha (SCA).

## <a name="install-fslogix"></a>Instalar fSLogix

Para instalar a FSLogix e o Editor de Regras, siga as instruções no [Download e instale o FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Criar e preparar um VHD para armazenar o Office

Em seguida, terá de criar e preparar uma imagem VHD para utilizar o Editor de Regras em:

1. Abra uma linha de comandos como administrador. e executar o seguinte comando:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Certifique-se de manter os espaços em branco que vê neste comando.

2. Em seguida, executar o seguinte comando:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Se encontrar o serviço, reinicie o VM antes de continuar com o passo 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Depois disso, vá a **Program Files**  >  **FSLogix**  >  **Apps** e execute o seguinte comando para criar o VHD alvo:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    O VHD que cria com este comando deve conter a pasta C: \\ Ficheiros de programa \\ Microsoft Office.

    >[!NOTE]
    >Se vir algum erro, desinstale o Office e comece de novo a partir do passo 1.

## <a name="configure-the-rule-editor"></a>Configure o Editor de Regras

Agora que preparou a sua imagem, terá de configurar o Editor de Regras e criar um ficheiro para armazenar as suas regras.

1. Vá a **Program Files**  >  **FSLogix**  >  **Apps** e execute **RuleEditor.exe**.

2. Selecione **'File**  >  **New**  >  **Create'** para fazer um novo conjunto de regras e, em seguida, guarde a regra definida para uma pasta local.

3. Selecione **O Conjunto de Regras Em** Branco e, em seguida, selecione **OK**.

4. Selecione o botão **+**. Isto abrirá a janela **'Adicionar Regra'.** Isto irá alterar as opções no diálogo **'Adicionar Regra'.**

5. A partir do menu suspenso, selecione **App Container (VHD) Rule**.

6. Introduza **C: \\ Ficheiros de programa \\ Do Microsoft Office** no campo **Pasta.**

7. Para o campo **de ficheiros Disk,** selecione **\<path\> \\ office.vhd** da secção **VHD alvo.**

8. Selecione **OK**.

9. Vá à pasta de trabalho em **C: \\ Utilizadores \\ \<username\> \\ Documenta \\ conjuntos de regras FSLogix** e procure os ficheiros .frx e .fxa. É necessário mover estes ficheiros para a pasta Regras localizada em **C: \\ Ficheiros de Programas \\ FSLogix \\ \\ Apps Rules** para que as regras comecem a funcionar.

10. Selecione **Aplicar Regras ao Sistema** para que as regras entrem em vigor.

     >[!NOTE]
     > Terá de aplicar os ficheiros de regras da aplicação para todos os anfitriões da sessão.

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre a FSLogix, consulte a nossa [documentação FSLogix.](/fslogix/)