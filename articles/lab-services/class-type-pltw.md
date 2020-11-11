---
title: Configurar laboratórios Project Lead the Way com serviços de laboratório Azure
description: Aprenda a criar laboratórios para ensinar as aulas do Project Lead the Way.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: 8585d09759319eef04da5ed68fec603cfa390093
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497271"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Configurar laboratórios para as aulas de Project Lead the Way

[Project Lead the Way (PLTW)](https://www.pltw.org/) é uma organização sem fins lucrativos que fornece currículo PreK-12 em toda a Área dos Estados Unidos em ciência da computação, engenharia e ciência biomédica.  Em cada aula de PLTW, os alunos usam uma variedade de aplicações de software como parte da sua experiência prática de aprendizagem.  Muitas das aplicações de software requerem um CPU rápido, ou em alguns casos, uma GPU.  Este artigo mostra-lhe como configurar laboratórios para as seguintes aulas de PLTW que são normalmente oferecidas aos alunos do 9º ao 12º ano:

- **Introdução ao Design de Engenharia**

    Os estudantes são introduzidos no processo de design de engenharia, que inclui a utilização do software [de design (CAD) assistido por computador da Autodesk](https://www.autodesk.com/products/inventor/new-features) para modelação 3D.

- **Princípios da Engenharia**
    
    Os alunos aprendem sobre mecanismos de engenharia, força estrutural\material e automação.  Esta classe usa software como [MD Solids,](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf) [West Point Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)e [America's Army Simulation](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Engenharia Civil e Arquitetura**

    Os alunos são ensinados a construir e a desenhar e desenvolvimento de sites usando o software de design de arquitetura [Revit da Autodesk](https://www.autodesk.com/products/revit/overview) para modelação de informação de construção 3D (BIM).

- **Fabricação Integrada de Computadores**

    Os estudantes exploram processos de fabrico modernos que envolvem robótica e automação.   Nesta aula, os alunos usam o software de fabrico (CAM) do [Inventor da Autodesk](https://www.autodesk.com/products/inventor/new-features) e [do Inventor da Autodesk.](https://www.autodesk.com/products/inventor-cam/overview) 

- **Eletrónica Digital**

    Os estudantes estudam circuitos e dispositivos de lógica electrónica usando o software de simulação e design [de circuitos multisim do Instrumento Nacional.](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html)

- **Design e Desenvolvimento de Engenharia**

    Os alunos contribuem para uma solução de ponta a ponta que combina investigação, design e teste que apresentam a um painel de engenheiros.  Nesta aula, os alunos usam o software [Cad inventor da Autodesk.](https://www.autodesk.com/products/inventor/new-features)

- **Essenciais da Ciência da Computação**

    Os alunos são introduzidos em conceitos e ferramentas computacionais.  Começam com a programação baseada em blocos e depois passam a utilizar a codificação baseada em texto utilizando ambientes de codificação como [os Blocos VEXcode V5](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Princípios da Ciência da Computação**
    
    Os estudantes aumentam a sua experiência de programação com [python](https://www.python.org/) usando [o ambiente de desenvolvimento do Código do Estúdio Visual da Microsoft.](https://code.visualstudio.com/) 

- **Ciência da Computação A**

    Os alunos aumentam assim a sua experiência de programação nesta aula através da aprendizagem do desenvolvimento de aplicações móveis.  Nesta aula, aprendem [Java](https://www.java.com/) usando [o ambiente de desenvolvimento do Código do Estúdio Visual da Microsoft.](https://code.visualstudio.com/)  Os estudantes também usam um emulador que lhes permite executar e testar o seu código de aplicação móvel.  Para obter informações sobre como configurar um emulador em Azure Labs, contacte-nos em azlabspilot@microsoft.com .

Consulte o site da PLTW para obter a [lista completa de software](https://www.pltw.org/pltw-software) para cada classe.

## <a name="lab-configuration"></a>Configuração de laboratório
Para configurar laboratórios para a PLTW, precisa de uma assinatura Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte o tutorial sobre [como configurar uma conta de laboratório.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account) Também pode usar uma conta de laboratório existente.

Uma vez que tenha uma conta de laboratório, deve criar laboratórios separados para cada sessão de uma aula de PLTW que a sua escola oferece.  Recomendamos também que crie imagens separadas para cada tipo de classe PLTW.  Para mais detalhes sobre como estruturar os seus laboratórios e imagens, leia a publicação do blog: [Movendo-se de um Laboratório Físico para serviços de laboratório Azure.](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)

### <a name="lab-account-settings"></a>Definições de conta de laboratório
Ativar as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como permitir imagens de marketplace, consulte o artigo sobre [como especificar as imagens do Marketplace disponíveis para os criadores de laboratório.](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)

| Definição de conta de laboratório | Instruções |
| -------------------- | ----- |
| Imagem do Marketplace | Ative a imagem do Windows 10 Pro para utilização na sua conta de laboratório. |

### <a name="lab-settings"></a>Configurações de laboratório
O tamanho do VM que recomendamos usar para as aulas de PLTW depende dos tipos de cargas de trabalho que os seus alunos estão a fazer na aula.  Para as classes acima, recomendamos a utilização de tamanhos VM de GPU grande e pequeno (visualização).  Consulte as orientações na tabela abaixo ao configurar laboratórios para as suas aulas de PLTW.

| Definição de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| **GPU Pequeno (Visualização)**.  Este VM é mais adequado para visualização remota, streaming, jogos, codificação utilizando quadros como OpenGL e DirectX.  Recomendamos a utilização deste tamanho para as seguintes classes PLTW: Engenharia Civil e Arquitetura, Eletrónica Digital, Fabrico Integrado de Computadores; e Engenharia Design e Desenvolvimento.
|Tamanho da máquina virtual| **Grande.**  Este tamanho é mais adequado para aplicações que precisam de CPUs mais rápidos, melhor desempenho do disco local, grandes bases de dados, caches de memória grandes.  Recomendamos a utilização deste tamanho para as seguintes aulas de PLTW: Introdução ao Design de Engenharia, Princípios de Engenharia, Informática, Princípios da Ciência da Computação e Ciência da Computação A.

### <a name="licensing-server"></a>Servidor de licenciamento
A maioria do software que é utilizado nas classes PLTW acima não *_requerem_* acesso a um servidor de licenciamento.  No entanto, terá de aceder a um servidor de licenciamento se pretender utilizar o modelo de licenciamento de rede da Autodesk para o seguinte software:
-   Revit
-   Inventor
-   Inventor CAM

Para utilizar o licenciamento de rede com o software da Autodesk, [a PLTW fornece passos detalhados](https://www.pltw.org/pltw-software) para instalar o Gestor de Licenças da Autodesk no seu servidor de licenciamento.  Este servidor de licenciamento está tipicamente localizado na sua rede no local ou alojado numa máquina virtual Azure (VM) dentro da rede virtual Azure (VNet).

Depois de configurar o servidor de licença, terá de [espreitar o VNet](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-connect-peer-virtual-network) para a sua [conta de laboratório.](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-lab-account) O espreitamento da rede deve ser feito _before* criar o laboratório para que as máquinas virtuais de laboratório possam aceder ao servidor de licenças e ao contrário.

Os ficheiros de licença gerados pelo Autodesk incorporam o endereço MAC do servidor de licenciamento.  Se decidir hospedar o seu servidor de licenciamento usando um VM Azure, é importante garantir que o endereço MAC do seu servidor de licenciamento não muda.   Caso contrário, quando o endereço MAC mudar, os seus ficheiros de licenciamento terão de ser regenerados.  Siga estas dicas para evitar que o seu endereço MAC mude:

- [Desconfie de um endereço IP e MAC estático para](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource#static-private-ip-and-mac-address) o Azure VM que acolhe o seu servidor de licenciamento.
- Certifique-se de que configura tanto a sua conta de laboratório como o VNet do servidor de licenciamento numa região\localização que tenha capacidade VM suficiente para que não tenha de mover estes recursos para uma nova região\localização mais tarde.

Leia também o artigo sobre [como configurar um servidor de licenciamento como um recurso partilhado](https://docs.microsoft.com/azure/lab-services/how-to-create-a-lab-with-shared-resource) para mais informações.

### <a name="template-machine"></a>Máquina de modelo
Alguns dos ficheiros de instalação que precisa para PLTW são grandes e demoram muito tempo a copiá-los quando os descarrega para a máquina de modelos de um laboratório.

Em vez de descarregar ficheiros de instalação na máquina de modelos e instalar tudo lá, recomendamos a criação das suas imagens PLTW no seu ambiente físico.  Em seguida, pode importar as imagens para a Galeria de Imagens Partilhadas para que possa usar estas imagens personalizadas para criar os seus laboratórios.  Leia o seguinte artigo para mais detalhes: [Faça o upload de uma imagem personalizada para a Galeria de Imagens Partilhadas](https://docs.microsoft.com/azure/lab-services/upload-custom-image-shared-image-gallery).

Seguindo esta recomendação, eis as principais tarefas para a criação de um laboratório:

1. No seu ambiente físico, crie a imagem para a classe.

    a.  Utilize os passos detalhados da PLTW para descarregar ficheiros de instalação e instalar o software necessário.

    > [!NOTE]    
    > Quando instala as aplicações do Autodesk, o computador que está a instalar o Autodesk tem de ser capaz de comunicar com o seu servidor de licenciamento (o assistente de instalação do Autodesk irá pedir-lhe que especifique o nome do computador da máquina onde o servidor de licença está hospedado).  Se estiver a hospedar o seu servidor de licenciamento num Azure VM, poderá ter de esperar para instalar o Autodesk na máquina de modelos do laboratório para que o assistente de instalação do Autodesk possa aceder ao seu servidor de licenciamento

    b.  [Instale e configuure o OneDrive](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-onedrive) (ou outras opções de back-up que a sua escola possa utilizar).
    
    c.  [Instale e configuize as atualizações do Windows](https://docs.microsoft.com/azure/lab-services/how-to-prepare-windows-template#install-and-configure-updates).

1.  Faça o upload da imagem personalizada para a [Galeria de Imagens Partilhadas que está anexada à sua conta de laboratório.](https://docs.microsoft.com/azure/lab-services/how-to-attach-detach-shared-image-gallery)

1.  Crie um laboratório e selecione a imagem personalizada que carregou no passo anterior.

1.  Após a criação do laboratório, inicie e ligue-se à máquina do modelo para validar os trabalhos de imagem como esperado.

1.  Finalmente, publique a máquina de modelo para criar os VMs dos alunos.

## <a name="student-devices"></a>Dispositivos estudantis
Os seus alunos podem ligar-se aos seus VMs de laboratório a partir de computadores Windows\Mac e Chromebooks.  Aqui estão os links para instruções para cada uma destas opções:

- [Conecte-se a partir de Janelas](https://docs.microsoft.com/azure/lab-services/how-to-use-classroom-lab#connect-to-the-vm)
- [Ligar a partir de Mac](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-mac-remote-desktop)
- [Conecte-se a partir do Chromebook](https://docs.microsoft.com/azure/lab-services/connect-virtual-machine-chromebook-remote-desktop)

## <a name="cost"></a>Custo
Vamos cobrir uma possível estimativa de custos para as classes pltw acima.  Esta estimativa não inclui o custo de executar um servidor de licenciamento ou para usar a Shared Image Gallery.  Usaremos uma turma de 25 alunos.  Há 20 horas de horário de aula.  Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora do horário de aulas.  Consulte as estimativas de custos abaixo para os tamanhos **gpu grande** e **pequeno (Visualização).**

- **VM grande**

    25 alunos x (20 horas programadas + 10 horas de quota) x 70 Unidades de Laboratório x 0,01 USD por hora = 525,00 USD

- **Pequeno GPU (Visualização)**

    25 alunos x (20 horas programadas + 10 horas de quota) x 160 Unidades de Laboratório x 0,01 USD por hora = 1200,00 USD

> [!IMPORTANT] 
> A estimativa de custos é apenas para fins.  Para obter detalhes atuais sobre os preços, consulte [o Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)

> [!NOTE] 
> Muitas das classes PLTW usam aplicações que são acedidas através de um navegador, como o MIT App Inventor.  Estas aplicações baseadas no navegador não requerem um CPU ou GPU rápido e podem ser acedidas a partir de qualquer dispositivo que tenha uma ligação à Internet.  Quando os estudantes estão a usar este tipo de aplicações, recomendamos que utilizem o navegador no seu dispositivo físico em vez de usarem o navegador nos seus VMs de laboratório.  Isto ajudará a reduzir os custos usando apenas os VMs de laboratório para aplicações que requerem um CPU ou GPU rápido.

## <a name="next-steps"></a>Passos seguintes
Os próximos passos são comuns para a criação de qualquer laboratório:

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Links de inscrição de e-mail para estudantes.](how-to-configure-student-usage.md#send-invitations-to-users) 