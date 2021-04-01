---
title: Criar um laboratório SOLIDWORKS para engenharia com a Azure Lab Services | Microsoft Docs
description: Aprenda a criar um laboratório para cursos de engenharia utilizando o SOLIDWORKS.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626109"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Criar um laboratório para aulas de engenharia utilizando a SOLIDWORKS

[A SOLIDWORKS](https://www.solidworks.com/) fornece um ambiente de design 3D assistido por computador (CAD) para modelar objetos sólidos e é utilizado em diferentes tipos de campos de engenharia.  Com a SOLIDWORKS, os engenheiros podem facilmente criar, visualizar, simular e documentar os seus desenhos.

Uma opção de licenciamento geralmente utilizada pelas universidades é o licenciamento da rede SOLIDWORKS.   Com esta opção, os utilizadores partilham um conjunto de licenças que são geridas por um servidor de licenciamento.  Este tipo de licença é por vezes chamado de licença "flutuante", porque só é necessário ter licenças suficientes para o número de utilizadores simultâneos.  Quando um utilizador é feito usando o SOLIDWORKS, a sua licença volta para o pool de licenças gerido centralmente para que possa ser reutilizado por outro utilizador.

Neste artigo, vamos mostrar como configurar uma classe que usa SOLIDWORKS 2019 e Licenciamento de Rede.

## <a name="license-server"></a>Servidor de licença

O licenciamento da rede SOLIDWORKS requer que tenha o Gestor de Licenças SolidNetWork instalado e ativado no seu servidor de licença.  Este servidor de licença está tipicamente localizado na sua rede no local ou numa rede privada dentro do Azure.  Para obter mais informações sobre como configurar o Gestor de Licenças SolidNetWork no seu servidor, consulte [instalar e ativar um Gestor de Licenças](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) no guia de instalação SOLIDWORKS.  Ao configurar isto, lembre-se do **número da porta** e do número de [**série**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) que são utilizados, uma vez que serão necessários em etapas posteriores.

Após a configuração do servidor de licença, terá de espreitar a [rede virtual (VNet)](./how-to-connect-peer-virtual-network.md) para a sua [conta de laboratório.](./tutorial-setup-lab-account.md)  O espreitamento da rede deve ser feito antes de criar o laboratório para que as máquinas virtuais de laboratório possam aceder ao servidor de licenças e ao contrário.

> [!NOTE]
> Deve verificar se as portas apropriadas são abertas nas suas firewalls para permitir a comunicação entre as máquinas virtuais do laboratório e o servidor de licença.  Por exemplo, consulte as instruções sobre modificar as [portas de computador do Gestor de Licenças para o Windows Firewall](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) que mostram como adicionar regras de entrada e saída à firewall do servidor de licença.  Também pode ter de abrir portas às máquinas virtuais do laboratório.  Siga os passos no artigo sobre [as definições de firewall para laboratórios](./how-to-configure-firewall-settings.md) para obter mais informações sobre isso, incluindo como obter o endereço IP público do laboratório.

## <a name="lab-configuration"></a>Configuração de laboratório

Para montar este laboratório, precisa de uma assinatura Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte o tutorial sobre [como configurar uma conta de laboratório.](./tutorial-setup-lab-account.md) Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Definições de conta de laboratório

Ativar as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como permitir imagens de marketplace, consulte o artigo sobre [como especificar as imagens do Marketplace disponíveis para os criadores de laboratório.](./specify-marketplace-images.md)

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Ative a imagem do Windows 10 Pro para utilização na sua conta de laboratório.|

> [!NOTE]
> Além do Windows 10, o SOLIDWORKS suporta outras versões do Windows.  Consulte [os requisitos do sistema SOLIDWORKS](https://www.solidworks.com/sw/support/SystemRequirements.html) para obter mais detalhes.

### <a name="lab-settings"></a>Configurações de laboratório

Utilize as definições na tabela abaixo ao configurar um laboratório de sala de aula. Para obter mais informações sobre como criar um laboratório de sala de aula, consulte a criação de um tutorial de laboratório em sala de aula.

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| **GPU Pequeno (Visualização)**.  Este VM é mais adequado para visualização remota, streaming, jogos, codificação utilizando quadros como OpenGL e DirectX.|  
|Imagem de máquina virtual| Windows 10 Pro|

> [!NOTE]
> O tamanho da máquina virtual **Small GPU (Visualização)** está configurado para permitir uma experiência gráfica de alto desempenho.  Para obter mais informações sobre este tamanho de máquina virtual, consulte o artigo sobre [como configurar um laboratório com GPUs](./how-to-setup-lab-gpu.md).

> [!WARNING]
> Não se esqueça de [espreitar a rede virtual da](./how-to-connect-peer-virtual-network.md) conta de laboratório para a rede virtual do servidor de **licenças antes** de criar o laboratório.

## <a name="template-virtual-machine-configuration"></a>Configuração da máquina virtual do modelo

Os passos desta secção mostram como configurar a sua máquina virtual de modelo, descarregando os ficheiros de instalação SOLIDWORKS e instalando o software do cliente:

1. Inicie a máquina virtual do modelo e ligue-se à máquina utilizando RDP.

1. Descarregue os ficheiros de instalação para software de cliente SOLIDWORKS. Tem duas opções para descarregar:
   - Descarregue a partir do portal de [clientes SOLIDWORKS.](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F)
   - Descarregue de um diretório num servidor.  Se utilizar esta opção, tem de se certificar de que o servidor está acessível a partir da máquina virtual do modelo.  Por exemplo, este servidor pode estar localizado na mesma rede virtual que é espreitada com a sua conta de laboratório.
  
    Para mais informações, consulte [a Instalação em Computadores Individuais no GUIA DE](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) INSTALAÇÃO SOLIDWORKS.

1. Assim que os ficheiros de instalação forem descarregados, instale o software do cliente utilizando o GESTOR DE Instalações SOLIDWORKS. Consulte os detalhes da [instalação de um cliente de licença](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) no guia de instalação SOLIDWORKS.

    > [!NOTE]
    > Na caixa de diálogo **Do Servidor de Adicionar,** será solicitado o número de **porta** utilizado para o seu servidor de licença e o nome ou endereço IP do servidor de licença.

## <a name="cost"></a>Custo

Vamos cobrir uma possível estimativa de custos para esta aula. Esta estimativa não inclui o custo de executar o servidor de licença. Usaremos uma turma de 25 alunos. Há 20 horas de horário de aula. Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora do horário de aulas programado. O tamanho da máquina virtual que escolhemos foi **a Small GPU (Visualization),** que é de 160 unidades de laboratório.

25 alunos \* (20 horas programadas + 10 horas de quota) \* 160 Unidades de Laboratório * 0,01 USD por hora = 1200,00 USD

>[!IMPORTANT]
> A estimativa de custos é apenas para fins.  Para obter detalhes atuais sobre os preços, consulte [o Preço dos Serviços do Laboratório Azure.](https://azure.microsoft.com/pricing/details/lab-services/)  

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)