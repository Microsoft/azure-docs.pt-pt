---
title: Compreender os relatórios de gestão de custos da Cloudyn no Azure
description: Este artigo ajuda a compreender a estrutura básica e as funções dos relatórios de gestão de custos da Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 9afe34097d005daeedf46fbaf30b37e53a8e3658
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132045"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Compreender os relatórios de gestão de custos da Cloudyn

Este artigo ajuda-o a compreender a estrutura básica e as funções dos relatórios de gestão de custos da Cloudyn. A maioria dos relatórios da Cloudyn são intuitivos e têm um aspeto e funcionalidade uniformes. Depois de ler este artigo, estará pronto para utilizar todos os relatórios de gestão de custos. Muitas funcionalidades padrão estão disponíveis em vários relatórios, o que lhe permite navegar facilmente nos relatórios. Os relatórios são personalizáveis e pode selecionar de entre várias opções para calcular e apresentar resultados.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-fields-and-options"></a>Campos e opções dos relatórios

Segue-se um exemplo do relatório Custos ao Longo do Tempo. A maioria dos relatórios da Cloudyn têm um esquema semelhante.

![Exemplo do relatório Custos ao Longo do Tempo com áreas numeradas correspondentes às descrições](./media/understanding-cost-reports/sample-report.png)

Cada área numerada na imagem anterior é descrita detalhadamente nas seguintes informações:

1. **Intervalo de Datas**

    Utilize a lista Intervalo de Datas para definir um intervalo de tempo do relatório com uma configuração predefinida ou personalizada.
2. **Filtro Guardado**

    Utilize a lista Filtro Guardado para guardar os grupos e os filtros atuais que são aplicados ao relatório. Os filtros guardados estão disponíveis nos relatórios dos custos e do desempenho, incluindo:

      - Análise de Custos
      - Alocação
      - Gestão de Recursos
      - Otimização

   Escreva um nome de filtro e clique em **Guardar**.

3. **Etiquetas**

    Utilize a área Etiquetas para agrupar por categorias de etiquetas. As etiquetas listadas no menu são do departamento ou do centro de custos do Azure, ou são da entidade de custos e de subscrição da Cloudyn. Selecione etiquetas para filtrar os resultados. Também pode escrever um nome de etiqueta (palavra-chave) para filtrar os resultados.

    ![Exemplo de uma lista de etiquetas para filtrar os resultados](./media/understanding-cost-reports/select-options.png)

    Clique em **Adicionar** para adicionar um novo filtro.

    ![Caixa Adicionar filtro a mostrar as opções e condições de filtragem](./media/understanding-cost-reports/add-filter.png)

    O agrupamento ou a filtragem de etiquetas não estão relacionados com a etiquetas de recursos ou de grupos de recursos do Azure.

    O agrupamento e a filtragem de etiquetas de alocação de custos estão disponíveis na opção de menu **Grupos**.

4. **Grupos nos relatórios**

    Utilize grupos nos relatórios Análise de Custos para mostrar categorias padrão e com itens a partir dos dados de faturação do relatório.  No entanto, os grupos nos relatórios Alocação de Custos mostram categorias baseadas em etiquetas. As categorias baseadas em etiquetas são definidas no modelo de alocação de custos e categorias com itens padrão a partir dos dados de faturação.

    ![Primeira lista de exemplo de etiquetas pelas quais pode agrupar](./media/understanding-cost-reports/groups-tags01.png)

    ![Segunda lista de exemplo de etiquetas pelas quais pode agrupar](./media/understanding-cost-reports/groups-tags02.png)

    Nos Relatórios Alocação de Custos, os grupos em categorias de grupos baseadas em etiquetas podem incluir:
      - Etiquetas
      - Etiquetas de grupos de recursos
      - Etiquetas de entidade de custos da Cloudyn
      - Categorias de etiquetas de subscrição para efeitos de alocação de custos

   Os exemplos podem incluir:
   - Centro de custos
   - Departamento
   - Aplicação
   - Ambiente
   - Código de custos

     Segue-se uma lista de grupos incorporados disponíveis nos relatórios:

     - **Tipo de Custo**
     - Selecione um tipo de custo, vários tipos de custos ou selecione todos. Os tipos de custos incluem:
       - Taxa Única
       - Suporte
       - Custo de Utilização
     - **Cliente**
       - Selecione um cliente específico, vários clientes ou selecione todos os clientes.
     - **Nome da Conta**
       - O nome da conta ou da subscrição. No Azure, trata-se do nome da subscrição do Azure.
     - **N.º de Conta**
       - Selecione uma conta, várias contas ou todas as contas. No Azure, trata-se do GUID da subscrição do Azure.
     - **Conta Principal**
       - Selecione a conta principal, várias contas ou selecione todas.
     - **Serviço**
       - Selecione um serviço, vários serviços ou selecione todos os serviços.
     - **Fornecedor**
       - O fornecedor de cloud onde os recursos e as despesas estão associados.
     - **Região**
       - Região do Azure na qual o recurso está alojado.
     - **Zona de Disponibilidade**
       - Localizações isoladas do AWS numa região.
     - **Tipo de Recurso**
       - O tipo de recurso em utilização.
     - **Subtipo**
       - Selecione o subtipo.
     - **Operação**
       - Selecione a operação ou **Mostrar tudo**.
     - **Modelo de Preços**
       - Com Custos Iniciais
       - Sem Custos Iniciais
       - Custos Iniciais Parciais
       - A Pedido
       - Reserva
       - Pontual
     - **Tipo de Custos**
       - Selecione o tipo de custo Negativo, Positivo ou ambos.
     - **Inquilinos**
       - Se um computador está em execução como computador dedicado.
     - **Tipo de Utilização**
       - O tipo de utilização pode ter taxas únicas ou recorrentes.

5. **Filtros**

    Utilize um filtro simples ou de seleção múltipla para definir os intervalos dos valores selecionados. Para definir um filtro, clique em **Adicionar** e, em seguida, selecione as categorias e os valores de filtro.

6. **Modelo de Custos**

    Utilize Modelo de Custos para selecionar um modelo de custos criado anteriormente com o Cost Allocation 360. Pode ter vários modelos de custos da Cloudyn, consoante os seus requisitos de alocação de custos. Algumas das suas equipas organizacionais podem ter requisitos de alocação de custos que diferem de outros. Cada equipa pode ter o seu próprio modelo de custos dedicado.

    Para obter informações sobre como criar uma definição de modelo de alocação de custos, veja [Utilizar etiquetas personalizadas para alocar custos](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortização**

    Utilize a Amortização nos relatórios Alocação de Custos para ver as taxas de serviço baseadas na não utilização ou os custos únicos a pagar. Os custos são distribuídos ao longo do tempo uniformemente durante todo o tempo de vida. Os exemplos de taxas únicas podem incluir:
    - Taxas de suporte anuais
    - Taxas de componentes de segurança anuais
    - Taxas de compra de instâncias reservadas
    - Alguns itens do Azure Marketplace.

   Em Amortização, selecione **Custo amortizado** ou **Custo real**.

8. **Resolução**

    Utilize a Resolução para selecionar a resolução de tempo dentro do intervalo de datas selecionado. A resolução de tempo determina a forma como as unidades são apresentadas no relatório e podem ser:
    - Diárias
    - Semanais
    - Mensalmente
    - Trimestrais
    - Anuais

9. **Regras de alocação**

    Utilize Regras de Alocação para aplicar ou desativar o recálculo da alocação de custos. Pode ativar ou desativar o recálculo da alocação de custos para os dados de faturação. O recálculo aplica-se às categorias selecionadas no relatório. Permite-lhe avaliar o impacto do recálculo da alocação de custos relativamente aos dados de faturação não processados.

10. **Não Categorizado**

    Utilize Não Categorizado para incluir ou excluir custos não categorizados no relatório.

11. **Mostrar/ocultar campos**

    A opção Mostrar/ocultar não tem qualquer efeito nos relatórios.

12. **Formatos de apresentação**

    Utilize Formatos de apresentação para selecionar várias vistas de gráficos ou de tabelas.

    ![Símbolos de formatos de apresentação que pode selecionar](./media/understanding-cost-reports/display-formats.png)

13. **Várias cores**

    Utilize Várias cores para definir a cor dos gráficos no relatório.

14. **Ações**

    Utilize Ações para guardar, exportar ou agendar o relatório.

15. **Política**

    Embora não estejam representados, alguns relatórios incluem uma política de cálculo de custos previstos. Nesses relatórios, a política **Consolidada** apresenta recomendações para todas as contas e subscrições na entidade atual, como a inscrição na Microsoft ou o contribuinte do AWS. A política **Autónoma** apresenta recomendações para uma conta ou subscrição como se não existissem outras subscrições. A política que selecionar varia consoante a estratégia de otimização utilizada pela sua organização. As projeções de custos baseiam-se nos últimos 30 dias de utilização.

## <a name="save-and-schedule-reports"></a>Guardar e agendar relatórios

Depois de criar um relatório, pode guardá-lo para utilização futura. Os relatórios guardados estão disponíveis em **As Minhas Ferramentas** > **Os Meus Relatórios**. Se fizer alterações a um relatório existente e o guardar, o relatório será guardado como uma nova versão. Em alternativa, pode guardá-lo como um novo relatório.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Guardar um relatório no portal da Cloudyn

Quando estiver a ver qualquer relatório, clique em **Ações** e, em seguida, selecione **Guardar nos meus relatórios**. Atribua um nome ao relatório e, em seguida, adicione o seu próprio URL ou utilize o URL criado automaticamente. Pode opcionalmente **Partilhar** o relatório publicamente com outras pessoas na sua organização ou pode partilhá-lo com a sua entidade. Se não partilhar o relatório, este continuará a ser um relatório pessoal que apenas pode ser visto por si. Guarde o relatório.


### <a name="save-a-report-to-cloud-provider-storage"></a>Guardar um relatório no armazenamento do fornecedor de cloud

Para guardar um relatório no fornecedor de serviços cloud, já deverá ter uma conta de armazenamento configurada. Quando estiver a ver um relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**. Atribua um nome ao relatório e, em seguida, adicione o seu próprio URL ou utilize o URL criado automaticamente. Selecione **Guardar no armazenamento** e, em seguida, selecione a conta de armazenamento ou adicione uma nova. Introduza um prefixo que seja anexado ao nome de ficheiro do relatório. Selecione o formato de ficheiro CSV ou JSON e, em seguida, guarde o relatório.

### <a name="schedule-a-report"></a>Agendar um relatório

Pode executar relatórios em intervalos agendados e enviá-los para uma lista de destinatários ou conta de armazenamento do fornecedor de serviços cloud. Quando estiver a ver um relatório, clique em **Ações** e, em seguida, selecione **Agendar relatório**. Pode enviar o relatório por e-mail e guardá-lo numa conta de armazenamento. Em **Agendar**, selecione o intervalo (diário, semanal ou mensal). Para semanal e mensal, selecione o dia ou datas de entrega e selecione a hora. Guarde o relatório agendado. Se selecionar o formato de relatório do Excel, o relatório será enviado como um anexo. Quando selecionar o formato de conteúdo de e-mail, os resultados do relatório apresentados no formato de gráfico são entregues como um gráfico.

### <a name="export-a-report-as-a-csv-file"></a>Exportar um relatório como um ficheiro CSV

Quando estiver a ver um relatório, clique em **Ações** e, em seguida, selecione **Exportar todos os dados do relatório**. É apresentada uma janela de pop-up e é transferido um ficheiro CSV.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os relatórios incluídos na Cloudyn em [Utilizar relatórios da Cloudyn](./use-reports.md).
- Saiba como utilizar relatórios para criar [dashboards](./dashboards.md).