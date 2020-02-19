---
title: Exemplo de tipos de classe em Serviços de Laboratório Azure  Microsoft Docs
description: Fornece alguns tipos de aulas para as quais pode configurar laboratórios usando serviços de laboratório azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 80204b6f156981ab3ecb8f348f3ce7ea077a6836
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443538"
---
# <a name="class-types-overview---azure-lab-services"></a>Visão geral dos tipos de classe - Serviços de Laboratório Azure

O Azure Lab Services permite-lhe configurar rapidamente ambientes de laboratório de sala de aula na nuvem. Os artigos desta secção fornecem orientações sobre como criar vários tipos de laboratórios de sala de aula utilizando os Serviços de Laboratório Azure.

## <a name="deep-learning-in-natural-language-processing"></a>Aprendizagem profunda no processamento de linguagem natural

Você pode configurar um laboratório focado na aprendizagem profunda no processamento de linguagem natural (NLP) usando o Azure Lab Services. O processamento de linguagem natural (NLP) é uma forma de inteligência artificial (IA) que permite computadores com capacidades de tradução, reconhecimento de fala e outras capacidades de compreensão linguística. Os alunos que têm uma aula de NLP recebem uma máquina virtual Linux (VM) para aprender a aplicar algoritmos de rede neural para desenvolver modelos de aprendizagem profunda que são usados para analisar a linguagem humana escrita.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [A criação de um laboratório focado na aprendizagem profunda no processamento de linguagem natural utilizando os Serviços de Laboratório Azure](class-type-deep-learning-natural-processing.md).

## <a name="shell-scripting-on-linux"></a>Script de Shell no Linux

Podes montar um laboratório para ensinar guiões de conchas no Linux. O scripting é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de amostragem, a classe abrange scripts tradicionais de bash e scripts melhorados. Scripts melhorados são scripts que combinam comandos de bash e Ruby. Esta abordagem permite que ruby passe dados ao redor e esmaga comandos para interagir com a concha.

Os alunos que têm estas aulas de scripting recebem uma máquina virtual Linux para aprender o básico do Linux, e também se familiarizarem com o script da concha da bash. A máquina virtual Linux vem com acesso remoto ao ambiente de trabalho ativado e com editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte a [Shell scripting no Linux](class-type-shell-scripting-linux.md).

## <a name="ethical-hacking"></a>Acesso ilícito ético

Podes montar um laboratório para uma aula que se centre no lado forense do hacking ético. Os testes de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta aceder ao sistema ou à rede para demonstrar vulnerabilidades que um intruso malicioso pode explorar.

Numa aula de hacking ético, os alunos podem aprender técnicas modernas para se defenderem contra vulnerabilidades. Cada aluno recebe uma máquina virtual do Windows Server que tem duas máquinas virtuais aninhadas – uma máquina virtual com imagem [Metasploitável3](https://github.com/rapid7/metasploitable3) e outra máquina com imagem [Kali Linux.](https://www.kali.org/) A máquina virtual Metasploitável é utilizada para fins de exploração.  A máquina virtual Kali Linux fornece acesso às ferramentas necessárias para executar tarefas forenses.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte A [criação de um laboratório para ensinar aulas de hacking ético.](class-type-ethical-hacking.md)

## <a name="database-management"></a>Gestão de bases de dados
Os conceitos de bases de dados são um dos cursos introdutórios ministrados na maioria dos departamentos de Ciências da Computação na faculdade. Pode criar um laboratório para uma aula básica de gestão de bases de dados nos Serviços de Laboratório Azure. Por exemplo, pode configurar um modelo de máquina virtual num laboratório com um Servidor de Base de Dados [MySQL](https://www.mysql.com/) ou um servidor [SQL Server 2019.](https://www.microsoft.com/sql-server/sql-server-2019)

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte A criação de um laboratório para ensinar a gestão de bases de dados para bases de [dados relacionais](class-type-database-management.md).

## <a name="python-and-jupyter-notebooks"></a>Cadernos Python e Jupyter
Você pode configurar uma máquina de modelo sintetizador a Azure Lab Services com as ferramentas necessárias para ensinar os alunos a usar [os Cadernos Jupyter](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código [fonte](https://www.python.org/) python executável em uma única tela chamada um caderno. A execução de um caderno resulta num registo linear de inputs e saídas.  Essas saídas podem incluir texto, tabelas de informação, lotes de dispersão, e muito mais.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte A criação de um laboratório para ensinar ciência de [dados com cadernos Python e Jupyter](class-type-jupyter-notebook.md).

## <a name="mobile-app-development-with-android-studio"></a>Desenvolvimento de aplicações móveis com o Android Studio
Você pode criar um laboratório em Azure Lab Services para ensinar uma aula de desenvolvimento de aplicações móveis introdutórias. Esta classe foca-se em aplicações móveis Android que podem ser publicadas na [Google Play Store.](https://play.google.com/store/apps)  Os alunos aprendem a usar o [Android Studio](https://developer.android.com/studio) para construir aplicações.  [O Emulador de Estúdio Visual para Android](https://visualstudio.microsoft.com/vs/msft-android-emulator/) é usado para testar a aplicação localmente.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [A criação de um laboratório para ensinar o desenvolvimento de aplicações móveis com o Android Studio](class-type-mobile-dev-android-studio.md).


## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos:

- [Criar um laboratório focado na aprendizagem profunda no processamento de linguagem natural usando serviços de laboratório azure](class-type-deep-learning-natural-processing.md)
- [Shell scripting em Linux](class-type-shell-scripting-linux.md)
- [Hacking ético](class-type-ethical-hacking.md)
