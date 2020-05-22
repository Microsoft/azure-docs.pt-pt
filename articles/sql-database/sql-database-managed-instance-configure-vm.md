---
title: Ligar vM do cliente - instância gerida
description: Ligue-se a uma instância gerida pela Base de Dados Azure SQL utilizando o Estúdio de Gestão de Servidores SQL a partir de uma máquina virtual Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: 2787756da048de0a4354d88feb8445c03fb3ef74
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773678"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>Quickstart: Configure Azure VM para ligar a uma instância gerida pela Base de Dados Azure SQL

Este quickstart mostra-lhe como configurar uma máquina virtual Azure para se ligar a uma instância gerida pela Base de Dados Azure SQL utilizando o SQL Server Management Studio (SSMS). Para um início rápido que mostre como se conectar a partir de um computador cliente no local usando uma ligação ponto-a-local, consulte [Configurar uma ligação ponto-a-local](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Pré-requisitos

Este quickstart utiliza os recursos criados na [Create a Managed Instance](sql-database-managed-instance-get-started.md) como ponto de partida.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Criar uma nova subrede na VNet de Instância Gerida

Os passos seguintes criam uma nova subrede no VNet de instância gerida para que uma máquina virtual Azure possa ligar-se à Instância Gerida. A subnet Managed Instance é dedicada a Instâncias Geridas. Não se pode criar outros recursos, como máquinas virtuais Azure, naquela sub-rede.

1. Abra o grupo de recursos para a Instância Gerida que criou no arranque rápido [create a Managed Instance.](sql-database-managed-instance-get-started.md) Selecione a rede virtual para a sua Instância Gerida.

   ![Recursos da Instância Gerida](./media/sql-database-managed-instance-configure-vm/resources.png)

2. Selecione **Subnets** e, em seguida, selecione **+ Subnet** para criar uma nova sub-rede.

   ![Subnets de instância gerida](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. Preencha o formulário utilizando as informações desta tabela:

   | Definição| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Nome** | Qualquer nome válido|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   | **Intervalo de endereços (bloco CIDR)** | Uma gama válida | O valor padrão é bom para este arranque rápido.|
   | **Grupo de segurança da rede** | Nenhum | O valor padrão é bom para este arranque rápido.|
   | **Tabela de rota** | Nenhum | O valor padrão é bom para este arranque rápido.|
   | **Pontos finais de serviço** | 0 selecionados | O valor padrão é bom para este arranque rápido.|
   | **Delegação de sub-rede** | Nenhum | O valor padrão é bom para este arranque rápido.|

   ![Nova subnet de instância gerida para vm cliente](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. Selecione **OK** para criar esta sub-rede adicional no VNet de instância gerida.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Criar uma máquina virtual na nova sub-rede na VNet

Os seguintes passos mostram-lhe como criar uma máquina virtual na nova subnet para ligar à Instância Gerida.

## <a name="prepare-the-azure-virtual-machine"></a>Prepare a máquina virtual Azure

Uma vez que a SQL Managed Instance é colocada na sua rede virtual privada, você precisa criar um Azure VM com uma ferramenta de cliente SQL instalada, como O Estúdio de Gestão de Servidores SQL ou Azure Data Studio. Esta ferramenta permite ligar-se à Instância Gerida e executar consultas. Este início rápido utiliza o SQL Server Management Studio.

A maneira mais fácil de criar uma máquina virtual cliente com todas as ferramentas necessárias é usar os modelos do Gestor de Recursos Azure.

1. Certifique-se de que está inscrito no portal Azure em outro separador de navegador. Em seguida, selecione o seguinte botão para criar uma máquina virtual cliente e instale o Estúdio de Gestão de Servidores SQL:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. Preencha o formulário utilizando as informações no quadro seguinte:

   | Definição| Valor sugerido | Descrição |
   | ---------------- | ----------------- | ----------- |
   | **Subscrição** | Uma subscrição válida | Deve ser uma subscrição na qual tem permissão para criar novos recursos. |
   | **Grupo de Recursos** |O grupo de recursos que especificou no início rápido da [Create Managed Instance.](sql-database-managed-instance-get-started.md)|Este grupo de recursos deve ser aquele em que existe o VNet.|
   | **Localização** | A localização do grupo de recursos | Este valor é povoado com base no grupo de recursos selecionado. |
   | **Nome da máquina virtual**  | Qualquer nome válido | Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming).|
   |**Nome de utilizador de administrador**|Qualquer nome de utilizador válido|Para nomes válidos, veja [Regras e restrições de nomenclatura](/azure/architecture/best-practices/resource-naming). Não utilize "serveradmin" pois é uma função reservada ao nível do servidor.<br>Utiliza este nome de utilizador sempre que [se conecta ao VM](#connect-to-virtual-machine).|
   |**Palavra-passe**|Qualquer palavra-passe válida|A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).<br>Utiliza esta palavra-passe sempre que [se conecta ao VM](#connect-to-virtual-machine).|
   | **Tamanho da máquina virtual** | Qualquer tamanho válido | O padrão neste modelo de **Standard_B2s** é suficiente para este arranque rápido. |
   | **Localização**|[resourceGroup().localização].| Não mude este valor. |
   | **Nome da rede virtual**|A rede virtual em que criou a Instância Gerida.|
   | **Nome da sub-rede**|O nome da sub-rede que criou no procedimento anterior| Não escolha a sub-rede em que criou a Instância Gerida.|
   | **Localização de artefactos** | [implementação().properties.templateLink.uri] | Não mude este valor. |
   | **artefactos Localização Sas token** | deixar em branco | Não mude este valor. |

   ![criar a VM cliente](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   Se usou o nome VNet sugerido e a subnet padrão na [criação da sua Instância Gerida,](sql-database-managed-instance-get-started.md)não precisa de alterar os dois parâmetros anteriores. Caso contrário, deverá alterar estes valores para os valores que inseriu quando configura o ambiente de rede.

3. Selecione o **I concordo com os termos e condições indicados acima da** caixa de verificação.
4. Selecione **Comprar** para implantar o Azure VM na sua rede.
5. Selecione o ícone **Notificações** para ver o estado de implantação.

> [!IMPORTANT]
> Não continue até aproximadamente 15 minutos após a criação da máquina virtual para dar tempo aos scripts pós-criação para instalar o Estúdio de Gestão de Servidores SQL.

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Os passos seguintes mostram como ligar à máquina virtual recentemente criada através de uma ligação de ambiente de trabalho remoto.

1. Após a conclusão da implementação, vá para o recurso de máquina virtual.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. Selecione **Ligar**.

   Um formulário de protocolo de ambiente de trabalho remoto (ficheiro.rdp) aparece com o endereço IP público e o número de porta para a máquina virtual.

   ![Formulário RDP](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. Selecione **Download RDP File**.

   > [!NOTE]
   > Também pode utilizar o SSH para se ligar ao seu VM.

4. Feche o **Formulário de Ligação à forma** da máquina virtual.
5. Para ligar à sua VM, abra o ficheiro RDP transferido.
6. Quando solicitado, selecione **Connect**. Num Mac, precisa de um cliente RDP como este [Cliente de Ambiente de Trabalho Remoto](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) a partir da Mac App Store.

7. Introduza o nome de utilizador e a palavra-passe especificado ao criar a máquina virtual e, em seguida, escolha **OK**.

8. Poderá receber um aviso de certificado durante o processo de início de sessão. Escolha **Sim** ou **Continue** a prosseguir com a ligação.

Está ligado à sua máquina virtual no painel do Gestor de Servidores.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>Utilize SSMS para ligar à Instância Gerida

1. Na máquina virtual, abra o Estúdio de Gestão de Servidores SQL (SSMS).

   Demora alguns momentos a abrir, pois precisa de completar a sua configuração, uma vez que esta é a primeira vez que o SSMS é iniciado.
2. Na caixa de diálogo **Connect to Server,** introduza o nome de **anfitrião** totalmente qualificado para a sua Instância Gerida na caixa de **nome** sinuosa. Selecione A autenticação do **servidor SQL,** forneça o seu nome de utilizador e palavra-passe e, em seguida, selecione **Connect**.

    ![ligação SSMS](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

Depois de ligar, pode ver as bases de dados do sistema e dos utilizadores no nó de bases de dados e vários objetos em nós de Segurança, Objetos de Servidor, Replicação, Gestão, SQL Server Agent e Gerador de Perfis de XEvent.

## <a name="next-steps"></a>Passos seguintes

- Para um início rápido que mostre como se conectar a partir de um computador cliente no local utilizando uma ligação ponto-a-local, consulte [Configurar uma ligação ponto-a-local](sql-database-managed-instance-configure-p2s.md).
- Para uma descrição geral das opções de ligação para aplicações, veja [Ligar as suas aplicações à Instância Gerida](sql-database-managed-instance-connect-app.md).
- Para restaurar uma base de dados existente do SQL Server a partir do local para uma instância gerida, pode utilizar o Serviço de Migração de Bases de [Dados Azure (DMS) para migração](../dms/tutorial-sql-server-to-managed-instance.md) ou o [comando RESTABELECIMENTO T-SQL](sql-database-managed-instance-get-started-restore.md) para restaurar a partir de um ficheiro de backup de base de dados.
