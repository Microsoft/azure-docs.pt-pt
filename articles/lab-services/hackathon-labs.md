---
title: Use serviços de laboratório Azure para hackathon
description: Este artigo descreve como usar os Serviços Azure Lab para criar laboratórios que pode usar para executar hackathons.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 561351636a37c2cd5c3070dbfef2a3122e5c29b0
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434265"
---
# <a name="use-azure-lab-services-for-your-next-hackathon"></a>Use os serviços de laboratório da Azure para o seu próximo hackathon
O Azure Lab Services foi concebido para ser leve e fácil de usar para que possa rapidamente girar um novo laboratório de máquinas virtuais (VMs) para o seu hackathon.  Utilize a seguinte lista de verificação para garantir que o seu hackathon corra o mais suavemente possível. Esta lista de verificação deve ser completada pelo seu departamento de TI ou pelo seu corpo docente responsável pela criação e gestão do seu laboratório de hackathon. 

Para utilizar os Serviços de Laboratório para o seu hackathon, certifique-se de que tanto a conta de laboratório como o seu laboratório são criados pelo menos alguns dias antes do início do seu hackathon. Além disso, siga as orientações abaixo:

## <a name="guidance"></a>Orientação

- **Crie o laboratório numa região ou local mais próximo dos participantes.** 

    Para reduzir a latência, crie o seu laboratório numa região mais próxima dos seus participantes de hackathon.  Se os seus participantes estão localizados em todo o mundo, você precisa usar o seu melhor julgamento para criar um laboratório que esteja localizado centralmente.  Ou, divida o hackathon para usar vários laboratórios com base nos locais onde os seus participantes estão localizados.
- **Escolha um tamanho computacional mais adequado para as necessidades de utilização.**

    Geralmente, quanto maior for o tamanho do cálculo, mais rápido será a máquina virtual. No entanto, para limitar os custos, terá de selecionar o tamanho do cálculo adequado com base nas necessidades dos seus participantes. Consulte [as informações de dimensionamento de VM no guia do administrador](administrator-guide.md#vm-sizing) para obter mais informações sobre os tamanhos de cálculo disponíveis.
- **Configure RDP\SSH para ligação remota ao ambiente de trabalho ao Linux VMs**.

    Se o hackathon utilizar VMs Linux, certifique-se de que o ambiente de trabalho remoto está ativado para que os seus participantes possam utilizar o RDP (protocolo de ambiente de trabalho remoto) ou o SSH (shell seguro) para se ligarem aos seus VMs. Este passo só é necessário para os VMs Linux e deve ser ativado ao criar o laboratório. Além disso, para RDP, poderá ser necessário instalar e configurar os pacotes de servidor RDP e GUI no modelo VM antes de publicar.  Para obter mais informações, consulte o guia de como fazer para [ativar o ambiente de trabalho remoto para o Linux](how-to-enable-remote-desktop-linux.md).

- **Instale e pare as atualizações do Windows**. 

    Se estiver a utilizar uma imagem do Windows, recomendamos que instale as atualizações mais recentes do Windows no [modelo VM](how-to-create-manage-template.md) do laboratório antes de a publicar para criar VMs de laboratório. É por razões de segurança e para evitar que os participantes sejam perturbados durante o hackathon para instalar atualizações, o que também pode fazer com que os seus VMs reiniciem. Também pode considerar desligar as atualizações do Windows para evitar futuras interrupções. Consulte o [guia de como instalar e configurar as atualizações do Windows](how-to-prepare-windows-template.md#install-and-configure-updates).
- **Decida como os alunos vão apoiar o seu trabalho.** 

    Cada um dos alunos é atribuído a uma máquina virtual para a vida do hackathon. Podem guardar o seu trabalho diretamente para a máquina, mas recomenda-se que os alunos apoiem o seu trabalho para que tenham acesso a ele depois do fim do hackathon. Por exemplo, devem guardar para uma localização externa, como OneDrive, GitHub, e assim por diante. Para utilizar o OneDrive, pode optar por configurá-lo automaticamente para os alunos nas suas máquinas virtuais de laboratório. Consulte o [guia de como instalar e configurar o OneDrive](how-to-prepare-windows-template.md#install-and-configure-onedrive).
- **Definir a capacidade VM de acordo com o número de participantes**. 

    Certifique-se de que a capacidade da máquina virtual do seu laboratório é definida com base no número de participantes que espera no seu hackathon. Ao publicar a máquina virtual do modelo, pode levar várias horas para criar todas as máquinas do laboratório. É por isso que recomendamos que o faça com antecedência para o início do hackathon. Para mais informações, consulte o [guia de como atualizar a capacidade do laboratório.](how-to-set-virtual-machine-passwords.md#update-the-lab-capacity)

- **Decida se restringe o acesso ao laboratório.** 

    Ao adicionar utilizadores ao laboratório, existe uma opção de acesso restrito que é ativada por padrão. Esta funcionalidade requer que adicione todos os e-mails dos seus participantes hackathon à lista antes de poder registar-se e aceder ao laboratório utilizando o link de registo. Se tiver um hackathon onde não sabe quem serão os participantes antes do evento, pode optar por desativar a opção de acesso restrito, que permite a qualquer pessoa registar-se no laboratório utilizando o link de registo. Para obter mais informações, consulte o [guia de como adicionar utilizadores.](how-to-configure-student-usage.md#add-users-to-a-lab)

- **Verifique as definições de horário, quota e autorredução**. 

    Os Serviços de Laboratório fornecem vários controlos de custos para limitar o uso de VMs. No entanto, se estas definições estiverem mal configuradas, podem fazer com que as máquinas virtuais do seu laboratório se desliguem inesperadamente. Para garantir que estas definições estão configuradas adequadamente para o seu hackathon, verifique as seguintes definições:

    **Horário**: Um [horário](how-to-create-schedules.md) permite controlar automaticamente quando as máquinas dos seus laboratórios são iniciadas e desligadas. Por predefinição, nenhum horário é configurado quando se cria um novo laboratório. No entanto, deve certificar-se de que a agenda do seu laboratório está definida de acordo com o que faz sentido para o seu hackathon.  Como exemplo, se o seu hackathon começar no sábado às 8:00 e terminar no domingo às 17:00 – poderá criar um horário que ligue automaticamente a máquina às 7:30 da manhã de sábado (cerca de 30 minutos antes do início do hackathon) e encerre-a às 17:00 de domingo. Em vez disso, também pode decidir não usar um horário.

    **Quota**: A [quota](how-to-configure-student-usage.md#set-quotas-for-users) controla o número de horas que os participantes terão acesso a uma máquina virtual fora do horário programado. Se a quota for alcançada enquanto um participante a estiver a utilizar, a máquina é automaticamente desligada e o participante não poderá reiniciá-la a menos que a quota seja aumentada. Por defeito, quando se cria um laboratório, a quota está definida para 10 horas. Mais uma vez, deve ter a certeza de definir a quota para que dê tempo suficiente para o hackathon, o que é especialmente importante se não criou um horário.

    **Autoshutdown**: Quando ativado, a definição [de autoshutdown](how-to-enable-shutdown-disconnect.md) faz com que as máquinas virtuais do Windows se desliguem automaticamente após um determinado período de tempo, uma vez que um aluno tenha desligado da sua sessão de PDR. Por predefinição, esta definição está desativada.

- **Configure as definições de firewall para permitir ligações aos VMs de laboratório**. 

    Certifique-se de que as definições de firewall da sua escola ou organização permitem a ligação aos VMs de laboratório utilizando RDP\SSH. Para obter mais informações, consulte o [guia de como configurar as definições de firewall da sua rede](how-to-configure-firewall-settings.md).

- **Instale o cliente RDP\SSH nos tablets dos participantes, Macs, PCs, e assim por diante.**

    Os participantes do Hackathon devem ter um cliente RDP e/ou SSH instalado nos seus tablets ou portáteis que utilizarão para se conectarem a VMs de laboratório. Pode escolher entre diferentes clientes RDP ou SSH, tais como:

    - Aplicação **de Conexão remota de ambiente de trabalho** da Microsoft para ligações RDP. A aplicação Remote Desktop Connection é suportada em diferentes tipos de plataformas, incluindo Chromebooks e [Mac.](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162)
    - [Massa](https://techcommunity.microsoft.com/t5/azure-lab-services/connecting-to-azure-lab-services-environments-on-your-macos/ba-p/1290162) para usar SSH para ligar a um Linux VM.
- **Verifique as máquinas virtuais de laboratório.** 

    Uma vez publicados VMs de laboratório, deve verificar se estão configurados corretamente. Basta fazer esta verificação para uma das máquinas virtuais do laboratório do participante:

    1. Ligue-se usando RDP e\ou SSH.
    2. Abra cada aplicação e ferramenta adicional que instalou para personalizar a imagem da máquina virtual base.
    3. Caminhe por alguns cenários básicos que sejam representativos das atividades que os participantes farão para garantir que o desempenho em VM é adequado com base no tamanho do cálculo selecionado.

## <a name="on-the-day-of-hackathon"></a>No dia do hackathon
Esta secção descreve os passos para completar o dia do seu hackathon.

1. **Iniciar VMs de laboratório.**

    Dependendo do seu sistema operativo, a sua máquina de laboratório pode demorar até 30 minutos a arrancar. Como resultado, é importante iniciar máquinas antes do hackathon começar para que os seus participantes não tenham de esperar. Se estiver a utilizar um horário, certifique-se de que os VMs são automaticamente iniciados pelo menos 30 minutos antes também.
2. **Convide os alunos a inscreverem-se e acederem à sua máquina virtual de laboratório.** 

    Forneça aos seus participantes as seguintes informações para que os participantes possam aceder aos seus VMs de laboratório. 

    - A ligação de registo do laboratório. 
    - Credenciais que devem ser utilizadas para ligar à máquina. Este passo só se aplica se o seu laboratório utilizar uma imagem baseada no Windows e configurar todos os VMs para usar a mesma palavra-passe.
    - Instruções sobre como os participantes SSH e\ou RDP para as suas máquinas.

        Para mais informações, consulte [o guia sobre o envio de convites aos utilizadores](how-to-configure-student-usage.md?branch=master#send-invitations-to-users) e a [ligação aos VMs do Linux.](how-to-use-remote-desktop-linux-student.md?branch=master) 

## <a name="next-steps"></a>Passos seguintes
Comece por criar uma conta de laboratório em laboratório seguindo as instruções do artigo: [Tutorial: Configurar uma conta de laboratório com os Serviços Azure Lab](tutorial-setup-lab-account.md).