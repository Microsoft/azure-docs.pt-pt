---
title: Ligação privada Azure para Azure Data Factory
description: Saiba como funciona a Azure Private Link na Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 5d13a6a77ede6277eebc7fdab7cd42165cb602fa
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746374"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Ligação privada Azure para Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Ao utilizar o Azure Private Link, pode ligar-se a várias plataformas como um serviço (PaaS) implementações em Azure através de um ponto final privado. Um ponto final privado é um endereço IP privado dentro de uma rede virtual específica e sub-rede. Para obter uma lista de implementações paaS que suportam a funcionalidade Private Link, consulte [a documentação private link](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Comunicação segura entre as redes de clientes e a Azure Data Factory 
Pode configurar uma rede virtual Azure como uma representação lógica da sua rede na nuvem. Ao fazê-lo, proporciona os seguintes benefícios:
* Ajudas a proteger os teus recursos Azure de ataques em redes públicas.
* Deixa as redes e a Data Factory comunicarem-se de forma segura entre si. 

Também pode ligar uma rede no local à sua rede virtual através da criação de uma ligação VPN (site-to-site) de segurança do Protocolo de Internet (site-to-site) ou uma ligação Azure ExpressRoute (peering privado). 

Também pode instalar um tempo de integração auto-hospedado numa máquina no local ou numa máquina virtual na rede virtual. Ao fazê-lo, permite-lhe:
* Executar atividades de cópia entre uma loja de dados em nuvem e uma loja de dados em uma rede privada.
* Despachar atividades de transformação contra recursos de computação numa rede no local ou numa rede virtual Azure. 

São necessários vários canais de comunicação entre a Azure Data Factory e a rede virtual do cliente, como mostra o seguinte quadro:

| Domínio | Porta | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Um avião de controlo, exigido pela data factory de autoria e monitorização. |
| `*.{region}.datafactory.azure.net` | 443 | Requerido pelo tempo de integração auto-hospedado para ligar ao serviço Data Factory. |
| `*.servicebus.windows.net` | 443 | Exigido pelo tempo de integração auto-hospedado para a autoria interativa. |
| `download.microsoft.com` | 443 | Requerido pelo tempo de integração auto-hospedado para descarregar as atualizações. |

Com o apoio do Private Link for Azure Data Factory, pode:
* Crie um ponto final privado na sua rede virtual.
* Ativar a ligação privada a uma instância específica da fábrica de dados. 

As comunicações ao serviço Azure Data Factory passam pelo Private Link e ajudam a fornecer conectividade privada segura. Não precisa de configurar o domínio e a porta anteriores numa rede virtual ou na sua firewall corporativa para fornecer uma forma mais segura de proteger os seus recursos.  

![Diagrama de Ligação Privada para a arquitetura da Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

Ativar o serviço Private Link para cada um dos canais de comunicação anteriores oferece a seguinte funcionalidade:
- **Apoiado:**
   - Pode ser autor e monitorizar a fábrica de dados na sua rede virtual, mesmo que bloqueie todas as comunicações de saída.
   - As comunicações de comando entre o tempo de integração auto-hospedado e o serviço Azure Data Factory podem ser executados de forma segura num ambiente de rede privada. O tráfego entre o tempo de integração auto-hospedado e o serviço Azure Data Factory passa por Private Link. 
- **Não suportado atualmente:**
   - A autoria interativa que utiliza um tempo de integração auto-hospedado, como a ligação de teste, a lista de pastas de navegação e a lista de tabelas, obter esquemas e dados de pré-visualização, passa pelo Private Link.
   - A nova versão do tempo de funcionaamento de integração auto-hospedado pode ser automaticamente descarregada a partir do Microsoft Download Center se ativar o Auto-Update.

   > [!NOTE]
   > Para funcionalidades que não são suportadas atualmente, ainda precisa configurar o domínio e a porta anteriormente mencionados na rede virtual ou na firewall corporativa. 

> [!WARNING]
> Quando criar um serviço ligado, certifique-se de que as suas credenciais estão guardadas num cofre de chaves Azure. Caso contrário, as credenciais não funcionarão quando ativar o Private Link na Azure Data Factory.

## <a name="dns-changes-for-private-endpoints"></a>Alterações no DNS para Pontos Finais Privados
Quando cria um ponto final privado, o registo de recursos DNS CNAME para a Data Factory é atualizado para um pseudónimo num subdomínio com o prefixo 'privatelink'. Por padrão, também criamos uma [zona privada de DNS,](https://docs.microsoft.com/azure/dns/private-dns-overview)correspondente ao subdomínio 'privatelink', com os registos de recursos DNS A para os pontos finais privados.

Quando resolve o URL de ponto final da fábrica de dados de fora do VNet com o ponto final privado, resolve-se para o ponto final público do serviço de fábrica de dados. Quando resolvido a partir do VNet que hospeda o ponto final privado, o URL do ponto final de armazenamento resolve-se para o endereço IP do ponto final privado.

Para o exemplo ilustrado acima, os registos de recursos DNS para a Data Factory 'DataFactoryA', quando resolvidos fora do VNet que alberga o ponto final privado, serão:

| Nome | Tipo | Valor |
| ---------- | -------- | --------------- |
| DataFactorya. {region}.datafactory.azure.net | CNAME   | DataFactorya. {region}.privatelink.datafactory.azure.net |
| DataFactorya. {region}.privatelink.datafactory.azure.net | CNAME   | < serviço de fábrica de dados > |
| < serviço de fábrica de dados >  | A | < serviço de fábrica de dados > de endereço IP público |

Os registos de recursos DNS para DataFactoryA, quando resolvidos no VNet que alberga o ponto final privado, serão:

| Nome | Tipo | Valor |
| ---------- | -------- | --------------- |
| DataFactorya. {region}.datafactory.azure.net | CNAME   | DataFactorya. {region}.privatelink.datafactory.azure.net |
| DataFactorya. {region}.privatelink.datafactory.azure.net   | A | < endereço IP de ponto final privado > |

Se estiver a utilizar um servidor DNS personalizado na sua rede, os clientes devem ser capazes de resolver o FQDN para o ponto final da Data Factory para o endereço IP do ponto final privado. Deverá configurar o seu servidor DNS para delegar o seu subdomínio de ligação privada para a zona privada de DNS para o VNet, ou configurar os registos A para ' DataFactoryA. {region}.privatelink.datafactory.azure.net' com o endereço IP do ponto final privado.

Para obter mais informações sobre a configuração do seu próprio servidor DNS para suportar pontos finais privados, consulte os seguintes artigos:
- [Name resolution for resources in Azure virtual networks](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server) (Resolução de nomes para recursos em redes virtuais do Azure)
- [Configuração DE DNS para pontos finais privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Configurar link privado para a fábrica de dados Azure
Pode criar pontos finais privados utilizando [o portal Azure](../private-link/create-private-endpoint-portal.md).

Pode escolher se liga o seu tempo de integração auto-hospedado à Azure Data Factory através de um ponto final público ou privado. 

![Screenshot de bloquear o acesso público do Tempo de Execução de Integração Auto-hospedado.](./media/data-factory-private-link/disable-public-access-shir.png)


Você também pode ir à sua fábrica de dados Azure no portal Azure e criar um ponto final privado, como mostrado aqui:

![Screenshot do painel "Ligações de ponto final privado" para criar um ponto final privado.](./media/data-factory-private-link/create-private-endpoint.png)

No passo de **Recurso,** selecione **Microsoft.Datafactory/factorys** como **tipo de recurso**. E se pretender criar um ponto final privado para comunicações de comando entre o tempo de funcionamento da integração auto-hospedada e o serviço Azure Data Factory, selecione a fábrica de **dados** como **sub-recurso target**.

![Screenshot do painel "Ligações de ponto final privado" para selecionar recursos.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> O desativação do acesso à rede pública é aplicável apenas ao tempo de funcionamento da integração auto-hospedada, e não ao tempo de execução da integração da integração do Azure Integration e aos Serviços de Integração de Servidores SQL (SSIS).

Se pretender criar um ponto final privado para a autoria e monitorização da fábrica de dados na sua rede virtual, selecione o **portal** como **sub-recurso Target**.

> [!NOTE]
> Ainda é possível aceder ao portal Azure Data Factory através de uma rede pública depois de criar um ponto final privado para o portal.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma fábrica de dados utilizando a UI da Azure Data Factory](quickstart-create-data-factory-portal.md)
- [Introdução ao Azure Data Factory](introduction.md)
- [Autoria visual na Azure Data Factory](author-visually.md)