---
title: Criar um laboratório para ensinar gestão de bases de dados para bases de dados relacionais Microsoft Docs
description: Aprenda a criar um laboratório para ensinar a gestão de bases de dados relacionais.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462417"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Criar um laboratório para ensinar gestão de bases de dados para bases de dados relacionais

Este artigo descreve como criar um laboratório para uma aula básica de gestão de bases de dados nos Serviços Azure Lab. Os conceitos de bases de dados são um dos cursos introdutórios ministrados na maioria dos departamentos de Ciências da Computação na faculdade. A Linguagem De Consulta Estruturada (SQL) é um padrão internacional. SQL é o idioma padrão para a gestão da base de dados de relação, incluindo a adição, acesso e gestão de conteúdos numa base de dados.  É mais notável pelo seu processamento rápido, fiabilidade comprovada, facilidade e flexibilidade de utilização.

Neste artigo, vamos mostrar como configurar um modelo de máquina virtual em laboratório com o MySQL Database Server e o servidor SQL Server 2019.  [MySQL](https://www.mysql.com/) é um sistema de gestão de bases de dados relacional de código aberto (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) é a versão mais recente do RDBMS da Microsoft.

## <a name="lab-configuration"></a>Configuração de laboratório

Para montar este laboratório, precisa de uma assinatura Azure e uma conta de laboratório para começar. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar. Assim que tiver uma assinatura Azure, pode criar uma nova conta de laboratório nos Serviços Azure Lab. Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [Tutorial para Configurar uma Conta de Laboratório.](tutorial-setup-lab-account.md)  Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Definições de conta de laboratório

Ativar as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como ativar imagens de marketplace, consulte [as imagens do Mercado Dese especificar disponíveis para os criadores de laboratório.](./specify-marketplace-images.md)

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Ative a imagem 'SQL Server 2019 Standard on Windows Server 2019' para utilização na sua conta de laboratório.|

### <a name="lab-settings"></a>Configurações de laboratório

Utilize as definições na tabela abaixo ao configurar um laboratório de sala de aula.  Para obter mais informações sobre como criar um laboratório de sala de aula, consulte [a criação de um tutorial de laboratório em sala de aula.](tutorial-setup-classroom-lab.md)

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ |
|Tamanho da máquina virtual| Média. Este tamanho é mais adequado para bases de dados relacionais, caching na memória e análise.|
|Imagem de máquina virtual| SQL Server 2019 Standard no Windows Server 2019|

## <a name="template-machine-configuration"></a>Configuração da máquina do modelo

Para instalar o MySQL no Windows Server 2019, pode seguir os passos mencionados no [Servidor Comunitário de Instalação e Execução do MySQL numa Máquina Virtual](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

O SQL Server 2019 está pré-instalado na imagem da máquina virtual que escolhemos ao criar o novo laboratório.

## <a name="cost-estimate"></a>Estimativa de custos

Vamos cobrir uma possível estimativa de custos para esta aula.  Usaremos uma turma de 25 alunos.  Há 20 horas de horário de aula.  Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora do horário de aulas programado.  O tamanho da máquina virtual que escolhemos era médio, que é 42 unidades de laboratório.

Aqui está um exemplo de uma possível estimativa de custos para esta classe:

25 alunos \* (20 horas programadas + 10 horas de quota) \* 0,42 USD por hora = 315,00 USD

Mais detalhes sobre os preços, consulte o [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão

Este artigo acompanhou-o através dos passos necessários para criar um laboratório para conceitos básicos de gestão de bases de dados usando tanto o MySQL como o SQL Server. Pode utilizar uma configuração semelhante para outras aulas de bases de dados.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)