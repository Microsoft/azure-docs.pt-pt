---
title: Atualização de versão principal - Portal Azure - Base de Dados Azure para MySQL - Servidor Único
description: Este artigo descreve como pode atualizar a versão principal para Azure Database para MySQL - Single Server usando o portal Azure
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 080d7c09b180d5943216793119718eb6a2add79e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95753056"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server-using-the-azure-portal"></a>Atualização principal da versão em Azure Database para MySQL Single Server usando o portal Azure

> [!IMPORTANT]
> A atualização da versão principal para a base de dados Azure para o MySQL Single Server está em pré-visualização pública.

Este artigo descreve como pode atualizar a sua versão principal do MySQL no local na Base de Dados Azure para o servidor único MySQL.

Esta funcionalidade permitirá que os clientes realizem atualizações in-place dos seus servidores MySQL 5.6 para o MySQL 5.7 com um clique de botão sem qualquer movimento de dados ou a necessidade de alterações nas cordas de ligação da aplicação.

> [!Note]
> * A atualização da versão principal só está disponível para atualização de versão principal do MySQL 5.6 para MySQL 5.7<br>
> * A atualização da versão principal ainda não está suportada no servidor de réplicas.
> * O servidor estará indisponível durante toda a operação de atualização. Por isso, é aconselhável efetuar atualizações durante a janela de manutenção planeada.

## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o MySQL Single Server](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57"></a>Execute a atualização da versão principal do MySQL 5.6 para MySQL 5.7

Siga estes passos para realizar a atualização da versão principal para o seu Azure Database do servidor MySQL 5.6

> [!IMPORTANT]
> Recomendamos realizar a atualização primeiro na cópia restaurada do servidor em vez de atualizar a produção diretamente. Veja [como realizar a restauração pontual](howto-restore-server-portal.md#point-in-time-restore). 

1. No [portal Azure,](https://portal.azure.com/)selecione a base de dados Azure existente para o servidor MySQL 5.6.

2. Na página **'Vista Geral',** clique no botão **'Upgrade'** na barra de ferramentas.

3. Na secção **De Atualização,** selecione **OK** para atualizar a base de dados Azure para o servidor MySQL 5.6 para o servidor 5.7.

    :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Azure Database for MySQL - visão geral - upgrade":::

4. Uma notificação confirmará que a atualização é bem sucedida.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**1. Quando é que esta funcionalidade de atualização será GA, uma vez que temos o MySQL v5.6 no nosso ambiente de produção que precisamos de atualizar?**

O GA desta funcionalidade está planeado antes da reforma do MySQL v5.6. No entanto, a funcionalidade está pronta e totalmente suportada pela Azure, pelo que deverá executá-la com confiança no seu ambiente. Como uma melhor prática recomendada, sugerimos fortemente que o execute e teste primeiro numa cópia restaurada do servidor para que possa estimar o tempo de inatividade durante a atualização e realizar um teste de compatibilidade da aplicação antes de o executar na produção. Para obter mais informações, consulte [como executar o restauro pontual](howto-restore-server-portal.md#point-in-time-restore) para criar uma cópia pontual do seu servidor. 

**2. Isto causará tempo de inatividade do servidor e, em caso afirmativo, quanto tempo?**

Sim, o servidor não estará disponível durante o processo de atualização, pelo que recomendamos que efetue esta operação durante a janela de manutenção planeada. O tempo de inatividade estimado depende do tamanho da base de dados, do tamanho do armazenamento previsto (IOPs previsto) e do número de tabelas na base de dados. O tempo de atualização é diretamente proporcional ao número de tabelas no servidor. Espera-se que as atualizações dos servidores Basic SKU levem mais tempo, uma vez que se encontra na plataforma de armazenamento padrão. Para estimar o tempo de inatividade para o ambiente do seu servidor, recomendamos que realize primeiro o upgrade na cópia restaurada do servidor.  

**3. Nota-se que ainda não é suportado no servidor de réplicas. O que quer dizer concreto?**

Atualmente, a atualização da versão principal não é suportada para o servidor de réplicas, o que significa que não deve executá-lo para servidores envolvidos na replicação (ou fonte ou servidor de réplica). Se quiser testar a atualização dos servidores envolvidos na replicação antes de adicionarmos o suporte de réplica para funcionalidade de atualização, recomendamos os seguintes passos:

1. Durante a manutenção planeada, [pare a replicação e elimine](howto-read-replicas-portal.md) o servidor de réplicas depois de capturar o seu nome e todas as informações de configuração (definições de firewall, configuração de parâmetros do servidor se for diferente do servidor de origem).
2. Execute a atualização do servidor de origem.
3. Provisão de um novo servidor de réplica de leitura com o mesmo nome e configuração capturadas no passo 1. O novo servidor de réplica estará em v5.7 automaticamente após o servidor de origem ser atualizado para v5.7.

**4. O que acontecerá se não optarmos por atualizar o nosso servidor MySQL v5.6 antes de 5 de fevereiro de 2021?**

Pode continuar a executar o seu servidor MySQL v5.6 como antes. O Azure **nunca realizará** uma atualização de força no seu servidor. No entanto, aplicar-se-ão as restrições documentadas na [Base de Dados Azure para a política de versão MySQL.](concepts-version-policy.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a política de versão Azure Database para a política de versão MySQL](concepts-version-policy.md).
