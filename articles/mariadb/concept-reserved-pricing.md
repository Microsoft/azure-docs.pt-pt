---
title: Pagar antecipadamente por computação com capacidade reservada-banco de dados do Azure para MariaDB
description: Pagar antecipadamente pelo banco de dados do Azure para recursos de computação MariaDB com capacidade reservada
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 5821fb03dcf4331cbab5744f056521468a56d5d2
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773112"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Pagar antecipadamente pelo banco de dados do Azure para recursos de computação MariaDB com capacidade reservada

O banco de dados do Azure para MariaDB agora ajuda a economizar dinheiro prestando por recursos de computação em comparação com os preços pagos conforme o uso. Com a capacidade reservada do banco de dados do Azure para MariaDB, você faz um compromisso antecipado em servidores MariaDB por um período de um ano para obter um desconto significativo nos custos de computação. Para comprar o banco de dados do Azure para a capacidade reservada do MariaDB, você precisa especificar a região do Azure, o tipo de implantação, o nível de desempenho e o termo. </br>

Você não precisa atribuir a reserva ao banco de dados do Azure específico para servidores MariaDB. Um banco de dados do Azure já em execução para MariaDB ou aqueles que foram implantados recentemente obterá automaticamente o benefício do preço reservado. Ao comprar uma reserva, você está pagando pelos custos de computação por um período de um ano. Assim que você comprar uma reserva, o banco de dados do Azure para cobranças de computação MariaDB que correspondam aos atributos de reserva não serão cobrados com as tarifas pagas conforme o uso. Uma reserva não abrange os encargos de software, rede ou armazenamento associados ao servidor de banco de dados MariaDB. No final do prazo de reserva, o benefício de cobrança expira e o banco de dados do Azure para MariaDB são cobrados pelo preço pago conforme o uso. As reservas não são renovadas automaticamente. Para obter informações sobre preços, consulte a [oferta de capacidade reservada do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/mariadb/). </br>

Você pode comprar o banco de dados do Azure para a capacidade reservada do MariaDB no [portal do Azure](https://portal.azure.com/). Para comprar a capacidade reservada:

* Você deve estar na função de proprietário de pelo menos uma assinatura corporativa ou individual com tarifas pagas conforme o uso.
* Para subscrições Enterprise, **Adicionar Instâncias Reservadas** tem de estar ativada no [EA Portal](https://ea.azure.com/). Ou, se essa configuração estiver desabilitada, você deverá ser um administrador de EA na assinatura.
* Para o programa CSP (provedor de soluções na nuvem), somente os agentes de administração ou os agentes de vendas podem comprar a capacidade reservada do banco de dados do Azure para MariaDB. </br>

Os detalhes sobre como clientes corporativos e clientes pagos conforme o uso são cobrados por compras de reserva, consulte [entender o uso de reserva do Azure para seu registro corporativo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e [entender o uso de reserva do Azure para sua assinatura pré-paga](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-server-size-before-purchase"></a>Determinar o tamanho do servidor correto antes da compra

O tamanho da reserva deve ser baseado na quantidade total de computação usada pela instância de bancos de dados existente ou em breve para ser implantada em uma região específica e usando o mesmo nível de desempenho e geração de hardware.</br>

Por exemplo, vamos supor que você esteja executando uma finalidade geral, Gen5 – 32 vCore MariaDB Database e dois bancos de dados com otimização de memória, Gen5 – 16 vCore MariaDB. Além disso, digamos que você planeja implantar no próximo mês uma finalidade geral adicional, Gen5 – servidor de banco de dados vCore de 32 e um servidor de banco de dados com otimização de memória, Gen5 – 16 vCore. Vamos supor que você saiba que precisará desses recursos por pelo menos 1 ano. Nesse caso, você deve comprar um vCores de 64 (2x32), reserva de 1 ano para o banco de dados único de uso geral-Gen5 e um 48 (2x16 + 16) reserva de 1 ano para a memória de banco de dados individual otimizada-Gen5


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Comprar o banco de dados do Azure para capacidade reservada do MariaDB

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **Todos os serviços** > **Reservas**.
3.  Selecione **Adicionar** e, em seguida, no painel reservas de compra, selecione **banco de dados do Azure para MariaDB** para comprar uma nova reserva para seus bancos de dados MariaDB.
4.  Preencha os campos obrigatórios. Bancos de dados novos ou existentes que correspondem aos atributos que você selecionar qualificar para obter o desconto de capacidade reservada. O número real do banco de dados do Azure para servidores MariaDB que obtém o desconto depende do escopo e da quantidade selecionada.


![Visão geral de preços reservados](media/concepts-reserved-pricing/mariadb-reserved-price.png)


A tabela a seguir descreve os campos obrigatórios.

| Campo | Descrição |
| :------------ | :------- |
| Subscrição   | A assinatura usada para pagar pelo banco de dados do Azure para reserva de capacidade reservada do MariaDB. O método de pagamento na assinatura é cobrado pelos custos antecipados para o banco de dados do Azure para reserva de capacidade reservada do MariaDB. O tipo de assinatura deve ser um Enterprise Agreement (números de oferta: MS-AZR-0017P ou MS-AZR-0148P) ou um contrato individual com preço pago conforme o uso (números de oferta: MS-AZR-0003P ou MS-AZR-0023P). Para uma subscrição Enterprise, os custos são deduzidos do saldo de fidelização monetária da inscrição ou cobrados como utilização excedida. Para uma assinatura individual com o preço pago conforme o uso, os encargos são cobrados no cartão de crédito ou no método de pagamento de fatura na assinatura.
| Âmbito | O escopo da reserva de vCore pode cobrir uma assinatura ou várias assinaturas (escopo compartilhado). Se você selecionar: </br></br> **Compartilhado**, o desconto de reserva vCore é aplicado ao banco de dados do Azure para servidores MariaDB em execução em qualquer assinatura em seu contexto de cobrança. Para clientes corporativos, o escopo compartilhado é o registro e inclui todas as assinaturas dentro do registro. Para clientes Pay As You Go, o âmbito partilhado é todas as subscrições Pay As You Go criadas pelo administrador de conta.</br></br> **Assinatura única**, o desconto de reserva vCore é aplicado ao banco de dados do Azure para servidores MariaDB nesta assinatura. </br></br> **Um grupo de recursos único**, o desconto de reserva é aplicado ao banco de dados do Azure para servidores MariaDB na assinatura selecionada e ao grupo de recursos selecionado dentro dessa assinatura.
| Região | A região do Azure que é coberta pelo banco de dados do Azure para reserva de capacidade reservada do MariaDB.
| Tipo de implantação | O banco de dados do Azure para o tipo de recurso MariaDB para o qual você deseja comprar a reserva.
| Escalão de Desempenho | A camada de serviço do banco de dados do Azure para servidores MariaDB.
| Termo | Um ano
| Quantidade | A quantidade de recursos de computação que está sendo adquirida no banco de dados do Azure para reserva de capacidade reservada do MariaDB. A quantidade é um número de vCores na região do Azure e no nível de desempenho selecionados que estão sendo reservados e receberão o desconto de cobrança. Por exemplo, se você estiver executando ou planejando executar um banco de dados do Azure para servidores MariaDB com a capacidade de computação total de Gen5 16 vCores na região leste dos EUA, você deverá especificar a quantidade como 16 para maximizar o benefício para todos os servidores.

## <a name="cancel-exchange-or-refund-reservations"></a>Cancelar, trocar ou reembolsar reservas

Pode cancelar, trocar ou reembolsar reservas com determinadas limitações. Para obter mais informações, veja [Trocas e reembolsos personalizados das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>flexibilidade do tamanho vCore

a flexibilidade do tamanho vCore ajuda você a escalar ou reduzir verticalmente dentro de um nível de desempenho e região, sem perder o benefício de capacidade reservada. 

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passos seguintes

O desconto de reserva vCore é aplicado automaticamente ao número do banco de dados do Azure para servidores MariaDB que correspondem ao banco de dados do Azure para atributos e escopo de reserva de capacidade reservada do MariaDB. Você pode atualizar o escopo do banco de dados do Azure para MariaDB reserva de capacidade reservada por meio de portal do Azure, PowerShell, CLI ou por meio da API. </br></br>
Para saber como gerenciar o banco de dados do Azure para capacidade reservada do MariaDB, consulte Gerenciar o banco de dados do Azure para capacidade reservada do MariaDB.

Para saber mais sobre as Reservas do Azure, veja os seguintes artigos:

* [O que são as reservas do Azure](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Gerir o Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Compreender o desconto das Reservas do Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Compreender a utilização de reservas na sua subscrição Pay As You Go](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mariadb)
* [Compreender a utilização de reservas na inscrição Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations no programa Fornecedor de Soluções Cloud (CSP) do Centro de Parceiros](https://docs.microsoft.com/partner-center/azure-reservations)
