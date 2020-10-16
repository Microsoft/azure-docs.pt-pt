---
title: Conectividade Configure Azure VM
titleSuffix: Azure SQL Managed Instance
description: Ligue-se ao Azure SQL Managed Instance utilizando o SQL Server Management Studio a partir de uma máquina virtual Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620261"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>Quickstart: Configurar um Azure VM para ligar à Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este quickstart mostra-lhe como configurar uma máquina virtual Azure para ligar ao Azure SQL Managed Instance usando o SQL Server Management Studio (SSMS). 


Para obter um arranque rápido que mostre como ligar a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart utiliza os recursos criados na [Criar uma instância gerida](instance-create-quickstart.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-vnet"></a>Criar uma nova sub-rede VNet

Os passos seguintes criam uma nova sub-rede no SQL Managed Instance VNet para que uma máquina virtual Azure possa ligar-se à instância gerida. A sub-rede SQL Managed Instance dedica-se a casos geridos. Não se pode criar outros recursos, como máquinas virtuais Azure, naquela sub-rede.

1. Abra o grupo de recursos para a instância gerida que criou no [Create a managed instance](instance-create-quickstart.md) quickstart. Selecione a rede virtual para a sua instância gerida.

   ![Recursos de instância gerida sql](./media/connect-vm-instance-configure/resources.png)

2. Selecione **Subnetas** e, em seguida, selecione **+ Sub-rede** para criar uma nova sub-rede.

   ![Subesí redes de instância gerida SQL](./media/connect-vm-instance-configure/subnets.png)

3. Preencha o formulário utilizando as informações desta tabela:

   | Definição| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Nome** | Qualquer nome válido|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   | **Intervalo de endereços (bloco CIDR)** | Uma gama válida | O valor predefinido é bom para este arranque rápido.|
   | **Grupo de segurança de rede** | Nenhum | O valor predefinido é bom para este arranque rápido.|
   | **Tabela de rotas** | Nenhum | O valor predefinido é bom para este arranque rápido.|
   | **Pontos finais de serviço** | 0 selecionados | O valor predefinido é bom para este arranque rápido.|
   | **Delegação de sub-rede** | Nenhum | O valor predefinido é bom para este arranque rápido.|

   ![Nova sub-rede sql de instância gerida para cliente VM](./media/connect-vm-instance-configure/new-subnet.png)

4. Selecione **OK** para criar esta sub-rede adicional no SQL Managed Instance VNet.

## <a name="create-a-vm-in-the-new-subnet"></a>Criar um VM na nova sub-rede 

Os passos seguintes mostram-lhe como criar uma máquina virtual na nova sub-rede para ligar ao SQL Managed Instance.

## <a name="prepare-the-azure-virtual-machine"></a>Prepare a máquina virtual Azure

Uma vez que a SQL Managed Instance é colocada na sua rede virtual privada, precisa de criar um Azure VM com uma ferramenta de cliente SQL instalada, como o SQL Server Management Studio ou o Azure Data Studio. Esta ferramenta permite ligar-se à SQL Managed Instance e executar consultas. Este início rápido utiliza o SQL Server Management Studio.

A forma mais fácil de criar uma máquina virtual cliente com todas as ferramentas necessárias é utilizar os modelos do Gestor de Recursos Azure.

1. Certifique-se de que está inscrito no portal Azure em outro separador do navegador. Em seguida, selecione o seguinte botão para criar uma máquina virtual do cliente e instale o SQL Server Management Studio:

   [![Imagem mostrando um botão marcado "Implementar para Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. Preencha o formulário utilizando as informações no quadro seguinte:

   | Definição| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Subscrição** | Uma subscrição válida | Deve ser uma subscrição na qual tem permissão para criar novos recursos. |
   | **Grupo de Recursos** |O grupo de recursos que especificou no arranque rápido de [Exemplos Geridos do SQL](instance-create-quickstart.md)|Este grupo de recursos deve ser aquele em que o VNet existe.|
   | **Localização** | A localização do grupo de recursos | Este valor é preenchido com base no grupo de recursos selecionado. |
   | **Nome da máquina virtual**  | Qualquer nome válido | Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   |**Nome de utilizador Admin**|Qualquer nome de utilizador válido|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). Não utilize "serveradmin", pois é um papel reservado ao nível do servidor.<br>Utilize este nome de utilizador sempre que [se ligar ao VM](#connect-to-the-virtual-machine).|
   |**Palavra-passe**|Qualquer palavra-passe válida|A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Utilize esta palavra-passe sempre que [ligar ao VM](#connect-to-the-virtual-machine).|
   | **Tamanho da máquina virtual** | Qualquer tamanho válido | O padrão neste modelo de **Standard_B2s** é suficiente para este arranque rápido. |
   | **Localização**|[grupo de recursos().localização].| Não mude este valor. |
   | **Nome da rede virtual**|A rede virtual em que criou o caso gerido|
   | **Nome da sub-rede**|O nome da sub-rede que criou no procedimento anterior| Não escolha a sub-rede em que criou a instância gerida.|
   | **Localização de artefactos** | [implementação.).properties.templateLink.uri] | Não mude este valor. |
   | **artefactos Localização Sas token** | Deixar em branco | Não mude este valor. |

   ![criar a VM cliente](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   Se utilizar o nome VNet sugerido e a sub-rede predefinida na [criação do seu SQL Managed Instance,](instance-create-quickstart.md)não precisa de alterar os dois últimos parâmetros. Caso contrário, deverá alterar estes valores para os valores que introduziu quando configurar o ambiente de rede.

3. Selecione o **eu concordo com os termos e condições indicados acima** da caixa de verificação.
4. **Selecione Comprar** para implementar o Azure VM na sua rede.
5. Selecione o ícone **'Notificações'** para visualizar o estado de implantação.

> [!IMPORTANT]
> Não continue até aproximadamente 15 minutos após a criação da máquina virtual para dar tempo aos scripts pós-criação para instalar o SQL Server Management Studio.

## <a name="connect-to-the-virtual-machine"></a>Ligar à máquina virtual

Os passos a seguir mostram-lhe como ligar-se à sua máquina virtual recém-criada utilizando uma ligação remote desktop.

1. Após a conclusão da implementação, vá para o recurso de máquina virtual.

    ![O Screenshot mostra o portal Azure com a página Overview para uma máquina virtual selecionada e o Connect em destaque.](./media/connect-vm-instance-configure/vm.png)  

2. Selecione **Ligar**.

   Um formulário de protocolo de ambiente de trabalho remoto (.ficheiro rdp) aparece com o endereço IP público e o número de porta para a máquina virtual.

   ![Formulário RDP](./media/connect-vm-instance-configure/rdp.png)  

3. Selecione **Transferir Ficheiro RDP**.

   > [!NOTE]
   > Também pode utilizar o SSH para ligar ao seu VM.

4. Feche o **Formulário de Máquina Virtual Connect.**
5. Para ligar à sua VM, abra o ficheiro RDP transferido.
6. Quando solicitado, selecione **Connect**. Num Mac, precisa de um cliente RDP, como [este Cliente de Desktop Remoto](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) da Mac App Store.

7. Introduza o nome de utilizador e a palavra-passe especificado ao criar a máquina virtual e, em seguida, escolha **OK**.

8. Poderá receber um aviso de certificado durante o processo de início de sessão. Escolha **Sim** ou **Continue** a proceder com a ligação.

Está ligado à sua máquina virtual no painel do Gestor do Servidor.

## <a name="connect-to-sql-managed-instance"></a>Ligar à Instância Gerida do SQL 

1. Na máquina virtual, abra o SQL Server Management Studio.

   Leva alguns momentos para abrir, pois precisa completar a sua configuração uma vez que esta é a primeira vez que sSMS é iniciado.
2. Na caixa de diálogo **'Ligar ao Servidor',** insira o **nome de anfitrião** totalmente qualificado para a sua instância gerida na caixa **de nomes do Servidor.** Selecione **A autenticação do servidor SQL,** forneça o seu nome de utilizador e senha e, em seguida, selecione **Connect**.

    ![SSMS ligam](./media/connect-vm-instance-configure/ssms-connect.png)  

Depois de ligar, pode ver as bases de dados do sistema e dos utilizadores no nó de bases de dados e vários objetos em nós de Segurança, Objetos de Servidor, Replicação, Gestão, SQL Server Agent e Gerador de Perfis de XEvent.

## <a name="next-steps"></a>Passos seguintes

- Para obter um arranque rápido que mostre como ligar a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [configurar uma ligação ponto-a-local](point-to-site-p2s-configure.md).
- Para obter uma visão geral das opções de ligação para aplicações, consulte [Conecte as suas aplicações à SQL Managed Instance](connect-application-instance.md).
- Para restaurar uma base de dados do SQL Server existente de um local para um caso gerido, pode utilizar o Serviço de [Migração da Base de Dados Azure para migração](../../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando T-SQL RESTORE](restore-sample-database-quickstart.md) para restaurar a partir de um ficheiro de backup de base de dados.
