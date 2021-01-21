---
title: Atualização principal da versão na Base de Dados Azure para MySQL - Servidor Único
description: Este artigo descreve como pode atualizar a versão principal para Azure Database para MySQL - Single Server
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630205"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Atualização principal da versão na Base de Dados Azure para MySQL Single Server

> [!IMPORTANT]
> A atualização da versão principal para a base de dados Azure para o MySQL Single Server está em pré-visualização pública.

Este artigo descreve como pode atualizar a sua versão principal do MySQL no local na Base de Dados Azure para o servidor único MySQL.

Esta funcionalidade permitirá que os clientes realizem atualizações in-place dos seus servidores MySQL 5.6 para o MySQL 5.7 com um clique de botão sem qualquer movimento de dados ou a necessidade de alterações nas cordas de ligação da aplicação.

> [!Note]
> * A atualização da versão principal só está disponível para atualização de versão principal do MySQL 5.6 para MySQL 5.7<br>
> * A atualização da versão principal ainda não está suportada no servidor de réplicas.
> * O servidor estará indisponível durante toda a operação de atualização. Por isso, é aconselhável efetuar atualizações durante a janela de manutenção planeada.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Execute uma grande atualização de versão do MySQL 5.6 para MySQL 5.7 utilizando o portal Azure

Siga estes passos para realizar a atualização de versão importante para o seu servidor Azure Database do mySQL 5.6 utilizando o portal Azure

> [!IMPORTANT]
> Recomendamos realizar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Veja [como realizar a restauração pontual](howto-restore-server-portal.md#point-in-time-restore).

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL 5.6.

2. Na página **'Vista Geral',** clique no botão **'Upgrade'** na barra de ferramentas.

3. Na secção **De Atualização,** selecione **OK** para atualizar a base de dados Azure para o servidor MySQL 5.6 para o servidor 5.7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - visão geral - upgrade":::

4. Uma notificação confirmará que a atualização é bem sucedida.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Execute uma grande atualização da versão do MySQL 5.6 para o MySQL 5.7 utilizando o Azure CLI

Siga estes passos para realizar uma grande atualização de versão para o seu servidor Azure Database do mySQL 5.6 utilizando O Azure CLI

> [!IMPORTANT]
> Recomendamos realizar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Veja [como realizar a restauração pontual](howto-restore-server-cli.md#server-point-in-time-restore).

1. Instale [o Azure CLI para windows](/cli/azure/install-azure-cli) ou utilize o Azure CLI em [Azure Cloud Shell](../cloud-shell/overview.md) para executar os comandos de atualização. 
 
   Esta atualização requer a versão 2.16.0 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. Execute o comando az version para localizar a versão e as bibliotecas dependentes instaladas. Para atualizar para a versão mais recente, execute o comando az upgrade.

2. Depois de iniciar sôm, execute o comando de atualização do [servidor az mysql:](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true)
    
   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   O pedido de comando mostra a mensagem "Running". Depois de esta mensagem já não ser apresentada, a atualização da versão está completa.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Quando é que esta funcionalidade de upgrade será GA, uma vez que temos o MySQL v5.6 no nosso ambiente de produção que precisamos de atualizar?

O GA desta funcionalidade está planeado antes da reforma do MySQL v5.6. No entanto, a funcionalidade está pronta e totalmente suportada pela Azure, pelo que deverá executá-la com confiança no seu ambiente. Como uma melhor prática recomendada, sugerimos fortemente que o execute e teste primeiro numa cópia restaurada do servidor para que possa estimar o tempo de inatividade durante a atualização e realizar um teste de compatibilidade da aplicação antes de o executar na produção. Para obter mais informações, consulte [como executar o restauro pontual](howto-restore-server-portal.md#point-in-time-restore) para criar uma cópia pontual do seu servidor. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Isto causará tempo de inatividade do servidor e, em caso afirmativo, quanto tempo?

Sim, o servidor não estará disponível durante o processo de atualização, pelo que recomendamos que efetue esta operação durante a janela de manutenção planeada. O tempo de inatividade estimado depende do tamanho da base de dados, do tamanho do armazenamento previsto (IOPs previsto) e do número de tabelas na base de dados. O tempo de atualização é diretamente proporcional ao número de tabelas no servidor. Espera-se que as atualizações dos servidores Basic SKU levem mais tempo, uma vez que se encontra na plataforma de armazenamento padrão. Para estimar o tempo de inatividade para o ambiente do seu servidor, recomendamos que realize primeiro o upgrade na cópia restaurada do servidor.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Nota-se que ainda não é suportado no servidor de réplicas. O que quer dizer concreto?

Atualmente, a atualização da versão principal não é suportada para o servidor de réplicas, o que significa que não deve executá-lo para servidores envolvidos na replicação (ou fonte ou servidor de réplica). Se quiser testar a atualização dos servidores envolvidos na replicação antes de adicionarmos o suporte de réplica para funcionalidade de atualização, recomendamos os seguintes passos:

1. Durante a manutenção planeada, [pare a replicação e elimine](howto-read-replicas-portal.md) o servidor de réplicas depois de capturar o seu nome e todas as informações de configuração (definições de firewall, configuração de parâmetros do servidor se for diferente do servidor de origem).
2. Execute a atualização do servidor de origem.
3. Provisão de um novo servidor de réplica de leitura com o mesmo nome e configuração capturadas no passo 1. O novo servidor de réplica estará em v5.7 automaticamente após o servidor de origem ser atualizado para v5.7.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>O que acontecerá se não optarmos por atualizar o nosso servidor MySQL v5.6 antes de 5 de fevereiro de 2021?

Pode continuar a executar o seu servidor MySQL v5.6 como antes. O Azure **nunca realizará** uma atualização de força no seu servidor. No entanto, aplicar-se-ão as restrições documentadas na [Base de Dados Azure para a política de versão MySQL.](concepts-version-policy.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a política de versão Azure Database para a política de versão MySQL](concepts-version-policy.md).
