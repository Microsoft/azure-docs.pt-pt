---
title: Crie um laboratório para ensinar o MATLAB com os Serviços de Laboratório Azure | Microsoft Docs
description: Aprenda a criar um laboratório para ensinar o MATLAB com os Serviços de Laboratório Azure.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445003"
---
# <a name="setup-a-lab-to-teach-matlab"></a>Criar um laboratório para ensinar o MATLAB

[O MATLAB,](https://www.mathworks.com/products/matlab.html)que representa o laboratório Matrix, é uma plataforma de programação da [MathWorks.](https://www.mathworks.com/)  Combina poder computacional e visualização tornando-a ferramenta popular nos campos da matemática, engenharia, física e química.

Se estiver a utilizar uma licença para todo o [campus,](https://www.mathworks.com/academia/tah-support-program/administrators.html)consulte as instruções para descarregar ficheiros de [instalação do MATLAB](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) para descarregar os ficheiros do instalador MATLAB na máquina de modelos.  

Neste artigo, vamos mostrar como configurar uma classe que usa software de cliente MATLAB com um servidor de licença.

## <a name="license-server"></a>Servidor de licença

Antes de modificar a máquina de modelo para o seu laboratório, terá de configurar o servidor para executar o software [Gestor de Licenças de Rede.](https://www.mathworks.com/help/install/administer-network-licenses.html)  Estas instruções só são aplicáveis às instituições que optam pela opção de licenciamento de rede para o MATLAB, que permite aos utilizadores partilhar um conjunto de chaves de licença.  Também terá de guardar o ficheiro da licença e a chave de instalação de ficheiros para mais tarde.  Para obter instruções detalhadas sobre como descarregar um ficheiro de licença, consulte o primeiro passo no Gestor de Licença de Rede de instalação com o artigo [de ligação à Internet.](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)

Instruções detalhadas para cobrir como instalar um servidor de licenciamento estão disponíveis na [instalação Do Gestor de Licenças de Rede com Ligação à Internet](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html).  Para ativar o empréstimo, consulte o artigo ['Licenciar Emprestado'.](https://www.mathworks.com/help/install/license/borrow-licenses.html)

Assumindo que o servidor de licença está localizado numa rede no local ou numa rede privada dentro do Azure, não se esqueça de [espreitar a rede virtual](how-to-connect-peer-virtual-network.md) para a sua [conta de laboratório.](tutorial-setup-lab-account.md)  O espreitamento da rede tem de ser feito antes de criar o laboratório para que as máquinas virtuais do laboratório possam aceder ao servidor de licenças.

## <a name="lab-configuration"></a>Configuração de laboratório

Para montar este laboratório, precisa de uma assinatura Azure para começar.  Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma subscrição do Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab ou utilizar uma conta existente.  Para criar uma nova conta de laboratório, consulte a [configuração de um tutorial de conta de laboratório.](tutorial-setup-lab-account.md)

Para criar um novo laboratório, acompanhe [um tutorial de laboratório em sala de aula.](tutorial-setup-classroom-lab.md)  Aplicar as seguintes definições:

| Tamanho da máquina virtual | Imagem |
| -------------------- | ----- |
| Médio | Windows 10 |

O MATLAB é suportado em mais sistemas operativos.  Consulte [os requisitos do sistema MATLAB](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) para obter mais detalhes.

> [!WARNING]
> Não se esqueça de [espreitar a rede virtual da](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) conta de laboratório para a rede virtual do servidor de licenças antes de criar o laboratório.

## <a name="template-machine"></a>Máquina de modelo

Após a criação da máquina do modelo, inicie a máquina e ligue-a para completar as seguintes tarefas principais.

1. Descarregue os ficheiros de instalação para o software do cliente MATLAB.
2. Instale o MATLAB utilizando a chave de instalação do ficheiro.

A instalação do MATLAB será um processo multi-partes.  A primeira parte irá descarregar os ficheiros do MATLAB e de quaisquer outros produtos que pretenda ser instalado.  A utilização de uma chave de instalação de ficheiros requer que todos os ficheiros de instalação dos produtos a instalar sejam pré-descarregados.  A segunda parte será instalar o software MATLAB no modelo VM e ativar o software.  Se o modelo VM estiver configurado para ativar usando o servidor da licença, os VMs do aluno farão o mesmo.

### <a name="download-installation-files"></a>Descarregue ficheiros de instalação

Deve ser administrador de licença para descarregar os ficheiros de instalação, bem como obter o ficheiro de licença e a chave de instalação de ficheiros.  As etapas para descarregar os ficheiros de instalação estão abaixo.

1. Inicie sessão na sua conta para [https://www.mathworks.com](https://www.mathworks.com) .
2. Escolha **a minha conta.**
3. Na secção **My Software** da página da conta, clique na licença anexa à configuração do Gestor de Licenças de Rede para o laboratório.
4. Na página de detalhes da licença, clique em **Baixar Produtos.**
5. Aguarde que o instalador se auto-extraia.
6. Inicie o instalador.  
7. Na **página 'Iniciar s-in' na sua página de Conta MathWorks,** insira a sua conta MathWorks.
8. Na página **Do Contrato de Licença mathWorks,** aceite o termo e clique no botão **Seguinte.**
9. Clique nas **Opções Avançadas** e escolha **que quero descarregar sem instalar**.
10. Na **pasta de destino Select**, clique em **Seguinte**.
11. Selecione o **Windows** como a plataforma do computador que vai instalar o MATLAB.
12. Na página **do produto Select,** certifique-se de que o MATLAB está selecionado juntamente com quaisquer outros produtos MathWorks que pretende instalar.
13. Na página **'Confirmar Seleções e Descarregar',** clique **em Iniciar Download**.  
14. Aguarde que os produtos selecionados descarreguem.  Clique em **Concluir**.

Também pode baixar uma imagem ISO a partir do site MathWorks.

1. Inicie sessão na sua conta para [https://www.mathworks.com](https://www.mathworks.com) .
2. Vai [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads) para.
3. Selecione a libertação do MATLAB que pretende instalar.
4. Clique no link "Get {version}.iso image" por baixo dos links relacionados onde {versão} é algo como R2020a.
5. Clique no link de **lançamento de descarregamento** azul para Windows.

### <a name="run-installer"></a>Instalador de execução

Uma vez descarregados os ficheiros, o segundo passo é executar o instalador. Mais uma vez, deve ser um administrador de licença para completar este passo.  Apenas os administradores de licença podem instalar o MATLAB com uma chave de instalação de ficheiros.

1. Verifique o ficheiro de licença descarregado e verifique se a linha SERVER lista corretamente o servidor de licença.  Para obter informações sobre como o ficheiro de licença deve ser formatado, consulte [a licença de rede de atualização,](https://www.mathworks.com/help/install/ug/network-license-files.html)o empréstimo de [licença](https://www.mathworks.com/help/install/license/borrow-licenses.html)e encontre artigos [de identificação de anfitrião.](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license)
2. Lance o Instalador MATLAB.
3. Na **página 'Iniciar s-in' na sua página de Conta MathWorks,** insira a sua conta MathWorks.
4. Na página **Do Contrato de Licença mathWorks,** aceite o termo e clique no botão **Seguinte.**
5. Clique nas **opções avançadas** e escolha eu tenho uma chave de **instalação de ficheiros.**
6. Na página 'Instalar' utilizando a página **'Chave de instalação de ficheiros',** insira a chave de instalação do ficheiro para o servidor de licença.   Clique em **Seguinte**.
7. Na página **'Select License',** navegue para o ficheiro de licença guardado ao descarregar os ficheiros de instalação anteriormente.
8. Na página **'Selecione',** clique em **Seguinte**.
9. Na página **'Produtos Selecionados',** clique em **'Seguinte'.**
10. Na página **'Escolha Seleções',** clique em **Seguinte**.
11. Na página **'Confirmar Seleções e Instalar',** clique **em Iniciar Instalação**.
12. Na página **'Instalação' Complete,** verifique **se o ATIVAR MATLAB** está verificado.  Clique em **Concluir**.

## <a name="cost-estimate"></a>Estimativa de custos

Vamos cobrir uma possível estimativa de custos para esta aula.  Esta estimativa não inclui o custo de funcionamento do servidor de licença.  Usaremos uma turma de 25 alunos.  Há 20 horas de horário de aula.  Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora do horário de aulas programado.  O tamanho da máquina virtual que escolhemos era médio, que é 55 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custos para esta classe:

25 alunos \* (20 horas programadas + 10 horas de quota) \* 55 unidades de laboratório \*  0,01 USD por hora = 412,50 USD

>[!IMPORTANT]
> A estimativa de custos é apenas para fins.  Para obter detalhes atuais sobre os preços, consulte [o Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)  

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar, gerir e publicar um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)
