---
title: Exemplos de tipos de classe em Azure Lab Services | Microsoft Docs
description: Fornece alguns tipos de aulas para as quais pode configurar laboratórios usando os Serviços Azure Lab.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a90fb128f5954f3eb713714ff22ff40a3beab36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627438"
---
# <a name="class-types-overview---azure-lab-services"></a>Visão geral dos tipos de classe - Azure Lab Services

Os Serviços de Laboratório Azure permitem-lhe configurar rapidamente ambientes de laboratório na nuvem. Os artigos nesta secção fornecem orientações sobre como configurar vários tipos de laboratórios usando os Serviços de Laboratório Azure.

## <a name="arcgis"></a>ArcGIS
[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) é um tipo de sistema de informação geográfica (SIG).  Pode configurar um laboratório que utilize as várias aplicações do ArcGIS Desktop, como [o ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) para fazer, editar e analisar mapas 2D.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [configurar um laboratório para o ArcMap\ArcGIS Desktop](class-type-arcgis.md).

## <a name="big-data-analytics"></a>Análise de macrodados
Pode criar um laboratório de GPU para ensinar uma grande aula de análise de dados. Com este tipo de aula, os alunos aprendem a lidar com grandes volumes de dados, e aplicam algoritmos de aprendizagem automática e estatística para obter insights de dados. Um objetivo fundamental para os alunos é aprender a usar ferramentas de análise de dados, como o pacote de software de código aberto da Apache Hadoop que fornece ferramentas para armazenar, gerir e processar grandes dados. 

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [configurar um laboratório para análise de big data utilizando a implementação de Docker da Plataforma de Dados HortonWorks](class-type-big-data-analytics.md).

## <a name="database-management"></a>Gestão de bases de dados
Os conceitos de bases de dados são um dos cursos introdutórios ministrados na maioria dos departamentos de Ciências da Computação na faculdade. Pode criar um laboratório para uma aula de gestão de bases de dados básicas nos Serviços de Laboratório Azure. Por exemplo, pode configurar um modelo de máquina virtual num laboratório com um [MySQL](https://www.mysql.com/) Database Server ou um servidor [SQL Server 2019.](https://www.microsoft.com/sql-server/sql-server-2019)

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [criar um laboratório para ensinar gestão de bases de dados para bases de dados relacionais.](class-type-database-management.md)

## <a name="deep-learning-in-natural-language-processing"></a>Aprendizagem profunda no processamento de linguagem natural
Você pode configurar um laboratório focado na aprendizagem profunda no processamento de linguagem natural (NLP) usando Azure Lab Services. O processamento de linguagem natural (NLP) é uma forma de inteligência artificial (IA) que permite aos computadores com capacidades de tradução, reconhecimento de voz e outras capacidades de compreensão da linguagem. Os alunos que fazem uma aula de NLP obtêm uma máquina virtual Linux (VM) para aprender a aplicar algoritmos de rede neural para desenvolver modelos de aprendizagem profunda que são usados para analisar linguagem humana escrita.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [configurar um laboratório focado na aprendizagem profunda no processamento de linguagem natural utilizando os Serviços Azure Lab](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>Acesso ilícito ético
Podes criar um laboratório para uma aula que se centre no lado forense da pirataria ética. Os testes de penetração, uma prática usada pela comunidade de hacking ético, ocorre quando alguém tenta aceder ao sistema ou rede para demonstrar vulnerabilidades que um intruso malicioso pode explorar.

Numa aula de hacking ético, os alunos podem aprender técnicas modernas para se defenderem contra vulnerabilidades. Cada aluno recebe uma máquina virtual de anfitrião do Windows Server que tem duas máquinas virtuais aninhadas – uma máquina virtual com imagem [Metaploitável3](https://github.com/rapid7/metasploitable3) e outra máquina com imagem [Kali Linux.](https://www.kali.org/) A máquina virtual metasploitável é utilizada para explorar propósitos.  A máquina virtual Kali Linux fornece acesso às ferramentas necessárias para executar tarefas forenses.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [criar um laboratório para ensinar aulas de hacking ético.](class-type-ethical-hacking.md)

## <a name="matlab"></a>MATLAB
[O MATLAB,](https://www.mathworks.com/products/matlab.html)que representa o laboratório Matrix, é uma plataforma de programação da [MathWorks.](https://www.mathworks.com/)  Combina poder computacional e visualização tornando-a ferramenta popular nos campos da matemática, engenharia, física e química.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [configurar um laboratório para ensinar o MATLAB.](class-type-matlab.md)

## <a name="networking-with-gns3"></a>Networking com GNS3
Você pode configurar um laboratório para uma aula que se foca em permitir que os alunos emular, configurar, testar e resolver problemas redes virtuais e reais usando software [GNS3.](https://www.gns3.com/) 

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [a Configuração de um laboratório para ensinar uma aula de networking.](class-type-networking-gns3.md)

## <a name="project-lead-the-way-pltw"></a>Projeto Liderar o Caminho (PLTW)
[Project Lead the Way (PLTW)](https://www.pltw.org/) é uma organização sem fins lucrativos que fornece currículo PreK-12 em toda a Área dos Estados Unidos em ciência da computação, engenharia e ciência biomédica.  Em cada aula de PLTW, os alunos usam uma variedade de aplicações de software como parte da sua experiência prática de aprendizagem.

Para obter informações detalhadas sobre como configurar este tipo de laboratórios, consulte [configurar laboratórios para as aulas de Project Lead the Way](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Cadernos Python e Jupyter
Você pode configurar uma máquina de modelo nos Serviços de Laboratório Azure com as ferramentas necessárias para ensinar os alunos a usar [cadernos Jupyter](http://jupyter-notebook.readthedocs.io). Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código fonte [Python](https://www.python.org/) executável numa única tela chamada caderno. A execução de um caderno resulta num registo linear de entradas e saídas.  Essas saídas podem incluir texto, tabelas de informação, parcelas de dispersão, e muito mais.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [criar um laboratório para ensinar ciência de dados com os Cadernos Python e Jupyter.](class-type-jupyter-notebook.md)

## <a name="shell-scripting-on-linux"></a>Script de Shell no Linux
Podes criar um laboratório para ensinar a escrever em Linux. A scripting é uma parte útil da administração do sistema que permite aos administradores evitar tarefas repetitivas. Neste cenário de amostra, a classe cobre scripts tradicionais de bash e scripts melhorados. Scripts melhorados são scripts que combinam comandos de bash e Ruby. Esta abordagem permite à Ruby passar dados e bater comandos para interagir com a concha.

Os alunos que tomam estas aulas de scripts recebem uma máquina virtual Linux para aprender o básico do Linux, e também se familiarizar com o script de conchas de bash. A máquina virtual Linux vem com acesso remoto ao ambiente de trabalho ativado e com editores de texto [gedit](https://help.gnome.org/users/gedit/stable/) e [Visual Studio Code](https://code.visualstudio.com/) instalados.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [a shell scripting no Linux.](class-type-shell-scripting-linux.md)

## <a name="solidworks-computer-aided-design-cad"></a>Conceção assistida por computador (CAD) do SolidWorks
Você pode criar um laboratório de GPU que dá aos estudantes de engenharia acesso à [SolidWorks.](https://www.solidworks.com/)  A SolidWorks proporciona um ambiente CAD 3D para modelar objetos sólidos.  Com a SolidWorks, os engenheiros podem facilmente criar, visualizar, simular e documentar os seus desenhos.

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [configurar um laboratório para aulas de engenharia utilizando a SolidWorks.](class-type-solidworks.md)

## <a name="sql-database-and-management"></a>Base de dados e gestão SQL
Linguagem de Consulta Estruturada (SQL) é o idioma padrão para a gestão de bases de dados relacionais, incluindo a adição, acesso e gestão de conteúdos numa base de dados.  Pode configurar um laboratório para ensinar conceitos de base de dados utilizando o servidor [MySQL](https://www.mysql.com/) Database e o servidor [SQL Server 2019.](https://www.microsoft.com/sql-server/sql-server-2019)

Para obter informações detalhadas sobre como configurar este tipo de laboratório, consulte [criar um laboratório para ensinar gestão de bases de dados para bases de dados relacionais.](class-type-database-management.md)

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Criar um laboratório focado na aprendizagem profunda no processamento de linguagem natural usando os Serviços Azure Lab](class-type-deep-learning-natural-language-processing.md)
- [Script de Shell no Linux](class-type-shell-scripting-linux.md)
- [Acesso ilícito ético](class-type-ethical-hacking.md)