---
title: Configurar laboratórios Project Lead The Way com serviços de laboratório Azure
description: Aprenda a criar laboratórios para ensinar as aulas do Project Lead The Way.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024624"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Configurar laboratórios para as aulas de Project Lead The Way

[Project Lead The Way (PLTW)](https://www.pltw.org/) é uma organização sem fins lucrativos que fornece currículos PreK &ndash; 12 em toda a Área dos Estados Unidos em ciência da computação, engenharia e ciência biomédica.  Em cada aula de PLTW, os alunos usam uma variedade de aplicações de software como parte da sua experiência prática de aprendizagem.  Muitas das aplicações de software requerem um CPU rápido ou, em alguns casos, uma GPU.  Este artigo mostra-lhe como configurar laboratórios para as seguintes aulas de PLTW, que são normalmente oferecidas aos alunos do 9º ano &ndash; 12:

- **Introdução ao Design de Engenharia**

    Os estudantes são introduzidos no processo de design de engenharia, que inclui a utilização de software [de design (CAD) assistido por computador autodesk inventor](https://www.autodesk.com/products/inventor/new-features) para modelação 3D.

- **Princípios da Engenharia**
    
    Os alunos aprendem sobre mecanismos de engenharia, força estrutural e material, e automação.  Esta classe usa software como [MD Solids,](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf) [West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)e [simulação do Exército da América.](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf)

- **Engenharia Civil e Arquitetura**

    Os alunos aprendem a construção e o design e desenvolvimento de sites utilizando o software de design de arquitetura [Autodesk Revit](https://www.autodesk.com/products/revit/overview) para modelação de informação de construção 3D (BIM).

- **Fabricação Integrada de Computadores**

    Os estudantes exploram processos de fabrico modernos que envolvem robótica e automação.   Nesta aula, os alunos utilizam o software de fabrico (CAM) [do Inventor Autodesk](https://www.autodesk.com/products/inventor/new-features) e [do Autodesk Inventor.](https://www.autodesk.com/products/inventor-cam/overview) 

- **Eletrónica Digital**

    Os estudantes estudam circuitos e dispositivos de lógica electrónica utilizando software de simulação e design [de circuitos de instrumentos nacionais.](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html)

- **Design e Desenvolvimento de Engenharia**

    Os alunos contribuem para uma solução de ponta a ponta, combinando pesquisa, design e testes que apresentam a um painel de engenheiros.  Nesta aula, os alunos usam o software [Autodesk Inventor CAD.](https://www.autodesk.com/products/inventor/new-features)

- **Essenciais da Ciência da Computação**

    Os alunos são introduzidos em conceitos e ferramentas computacionais.  Começam com a programação baseada em blocos e depois passam para a codificação baseada em texto, utilizando ambientes de codificação como [os blocos VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Princípios da Ciência da Computação**
    
    Os alunos aumentam a sua experiência de programação com [python](https://www.python.org/) utilizando o ambiente de desenvolvimento do [Microsoft Visual Studio Code.](https://code.visualstudio.com/) 

- **Ciência da Computação A**

    Os alunos expandem a sua competência de programação nesta aula através da aprendizagem do desenvolvimento de aplicações móveis.  Nesta aula, aprendem [Java](https://www.java.com/) utilizando o ambiente de desenvolvimento do [Microsoft Visual Studio Code.](https://code.visualstudio.com/)  Os estudantes também usam um emulador que lhes permite executar e testar o seu código de aplicação móvel.  Para obter informações sobre como configurar um emulador nos Serviços Azure Lab, [contacte a Azure Lab Services](mailto:AzLabsCOVIDSupport@microsoft.com).

Para obter uma lista completa de software de classe, vá ao site da [PLTW](https://www.pltw.org/pltw-software) para cada aula.

## <a name="lab-configuration"></a>Configuração de laboratório

Para começar a configurar laboratórios para a PLTW, precisa de uma assinatura Azure e uma conta de laboratório. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. 

Depois de obter uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [configurar uma conta de laboratório.](./tutorial-setup-lab-account.md) Também pode usar uma conta de laboratório existente.

Depois de criar uma conta de laboratório, deve criar um laboratório separado para cada sessão de aula de PLTW que a sua escola oferece.  Recomendamos também que crie imagens separadas para cada tipo de classe PLTW.  Para obter mais informações sobre como estruturar os seus laboratórios e imagens, consulte o post do blog [Move from a Physical Lab to Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Definições de conta de laboratório

Ative as definições da sua conta de laboratório como descrito na tabela seguinte. Para obter mais informações sobre como ativar as imagens do Azure Marketplace, consulte [especificar as imagens do Azure Marketplace disponíveis para os criadores de laboratório.](./specify-marketplace-images.md)

| Definição de conta de laboratório | Instruções |
| -------------------- | ----- |
| Imagem do Marketplace | Ative a imagem do Windows 10 Pro para utilização na sua conta de laboratório. |

<br>

### <a name="lab-settings"></a>Configurações de laboratório
O tamanho da máquina virtual (VM) que recomendamos usar para as aulas de PLTW depende dos tipos de cargas de trabalho que os seus alunos estão a fazer na aula.  Para as classes anteriores, recomendamos a utilização de tamanhos de GPU Pequeno (Visualização) e Grandes Tamanhos VM. Ao configurar laboratórios para as suas aulas de PLTW, consulte as orientações na tabela seguinte:

| Definição de laboratório | Valor e descrição | Recomendação de classe |
| ------------ | ------------------ | --- |
| Tamanho da máquina virtual | **Pequeno GPU (Visualização)**<br>Mais adequado para visualização remota, streaming, jogos e codificação com estruturas como OpenGL e DirectX. | Recomendamos a utilização deste tamanho para as seguintes aulas de PLTW: Engenharia Civil e Arquitetura, Eletrónica Digital, Fabricação Integrada de Computadores e Engenharia e Desenvolvimento.
| Tamanho da máquina virtual | **Grande**<br>Mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados e caches de memória grandes. | Recomendamos a utilização deste tamanho para as seguintes aulas de PLTW: Introdução ao Design de Engenharia, Princípios de Engenharia, Informática, Princípios da Ciência da Computação e Ciência da Computação A. |

<br>

### <a name="license-server"></a>Servidor de licença
A maioria do software que é usado nas classes PLTW anteriormente mencionadas *não* requer acesso a um servidor de licença.  No entanto, terá de aceder a um servidor de licença se pretender utilizar o modelo de licenciamento da rede Autodesk para o seguinte software:
-   Revit
-   Inventor
-   Inventor CAM

Para utilizar o licenciamento de rede com o software Autodesk, [a PLTW fornece passos detalhados](https://www.pltw.org/pltw-software) para instalar o Gestor de Licenças de Rede Autodesk no seu servidor de licença.  Este servidor de licença está normalmente localizado na sua rede no local ou alojado numa máquina virtual Azure (VM) dentro da rede virtual Azure.

Depois de configurar o servidor de licença, terá de [espreitar a rede virtual](./how-to-connect-peer-virtual-network.md) com a sua [conta de laboratório.](./tutorial-setup-lab-account.md) Tens de fazer o olhar da rede *antes* de criares o laboratório para que os teus VMs de laboratório possam aceder ao servidor da licença e vice-versa.

Os ficheiros de licença gerados por automáticos incorporam o endereço MAC do servidor de licença.  Se decidir hospedar o seu servidor de licença utilizando um VM Azure, é importante certificar-se de que o endereço MAC do seu servidor de licença não muda. Se o endereço MAC mudar, terá de regenerar os seus ficheiros de licenciamento. Para evitar que o seu endereço MAC mude, faça o seguinte:

- [Desconfie de um endereço IP e MAC estático para](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) o Azure VM que acolhe o seu servidor de licença.
- Certifique-se de configurar tanto a sua conta de laboratório como a rede virtual do servidor de licenças numa região ou local que tenha capacidade VM suficiente para que não tenha de mover estes recursos para uma nova região ou localização mais tarde.

Para obter mais informações, consulte [Configurar um servidor de licença como um recurso partilhado.](./how-to-create-a-lab-with-shared-resource.md)

### <a name="template-machine"></a>Máquina de modelo
Alguns dos ficheiros de instalação que precisa para PLTW são grandes. Quando descarrega os ficheiros para um VM modelo de laboratório, eles podem demorar muito tempo a copiar.

Em vez de descarregar ficheiros de instalação para a máquina de modelos e instalar tudo lá, recomendamos a criação das suas imagens PLTW no seu ambiente físico.  Em seguida, pode importar as imagens personalizadas para a Galeria de Imagens Partilhadas para que possa usá-las para criar os seus laboratórios.  Para mais informações, consulte [o Upload de uma imagem personalizada para a Galeria de Imagens Partilhadas.](./upload-custom-image-shared-image-gallery.md)

Ao seguir esta recomendação, observe as principais tarefas para a criação de um laboratório:

1. No seu ambiente físico, crie a imagem para a classe.

    a.  Utilize os passos detalhados da PLTW para descarregar os ficheiros de instalação e instalar o software necessário.

    > [!NOTE]    
    > Quando instala as aplicações Autodesk, o computador em que as instala tem de ser capaz de comunicar com o servidor da sua licença. O assistente de instalação Autodesk irá solicitar-lhe que especifique o nome do computador da máquina em que o servidor de licença está hospedado.  Se estiver a hospedar o seu servidor de licença num Azure VM, poderá ter de esperar para instalar o Autodesk no VM do modelo de laboratório para que o assistente de instalação possa aceder ao servidor da licença.

    b.  [Instale e configuure o OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) ou outras opções de backup que a sua escola possa utilizar.
    
    c.  [Instale e configuize as atualizações do Windows](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Faça o upload da imagem personalizada para a [galeria de imagens partilhada que está anexada à sua conta de laboratório.](./how-to-attach-detach-shared-image-gallery.md)

1.  Crie um laboratório e, em seguida, selecione a imagem personalizada que carregou no passo anterior.

1.  Após a criação do laboratório, inicie e ligue-se ao modelo VM para validar que a imagem funciona como esperado.

1.  Finalmente, publique o modelo VM para criar os VM dos alunos.

## <a name="student-devices"></a>Dispositivos estudantis
Os alunos podem ligar-se aos seus VMs de laboratório a partir de computadores Windows, Mac e Chromebook. Para obter instruções, veja:

- [Conecte-se a partir de Janelas](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Ligar a partir de Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Conecte-se a partir do Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Custo
Vamos cobrir uma estimativa de custos por exemplo para as classes PLTW.  Esta estimativa não inclui o custo de executar um servidor de licença ou usar uma galeria de imagens partilhada. Suponha que tenha uma turma de 25 alunos, cada um dos quais tem 20 horas de horário de aula.  Cada aluno também tem mais 10 horas de quota para trabalhos de casa ou tarefas fora do horário de aulas.  Aqui estão os custos estimados:

- **VM grande**

    25 alunos &times; (20 horas programadas + 10 horas de quota) &times; 70 Unidades de Laboratório &times; USD0,01 por hora = USD525,00

- **Pequena GPU (visualização)**

    25 alunos &times; (20 horas programadas + 10 horas de quota) &times; 160 Unidades de Laboratório &times; USD0,01 por hora = USD1200,00

> [!IMPORTANT] 
> A estimativa de custos é apenas para fins.  Para obter informações sobre preços atuais, consulte [os preços dos Serviços de Laboratório Azure](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Muitas das classes PLTW usam aplicações que são acedidas através de um navegador, como o MIT App Inventor.  Estas aplicações baseadas no navegador não requerem um CPU ou GPU rápido, e você pode acessá-las a partir de qualquer dispositivo que tenha uma ligação à Internet.  Quando os estudantes estão a usar este tipo de aplicações, recomendamos que utilizem o navegador no seu dispositivo físico, em vez do navegador no seu VM de laboratório. Os alunos podem ajudar a reduzir os custos usando o seu VM de laboratório apenas para aplicações que requerem um CPU rápido ou GPU.

## <a name="next-steps"></a>Passos seguintes

Ao configurar o seu laboratório, veja os seguintes artigos:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quotas](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users) 
