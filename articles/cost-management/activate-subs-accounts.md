---
title: Ativar subscrições e contas do Azure | Microsoft Docs
description: Ative o acesso através das APIs do Azure Resource Manager para contas novas e existentes, e resolva problemas comuns com contas.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2018
ms.topic: quickstart
ms.service: cost-management
manager: vitavor
ms.custom: secdec18
ms.openlocfilehash: cc4422c4a22309eaf78a3eb9439a5cd27f664863
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997654"
---
# <a name="activate-azure-subscriptions-and-accounts-with-cloudyn"></a>Ativar subscrições e contas do Azure com o Cloudyn

Adicionar ou atualizar as suas credenciais do Azure Resource Manager permite que o Cloudyn detete todas as contas e subscrições no seu Inquilino do Azure. Se também tiver ativado a extensão do Diagnóstico do Azure nas suas máquinas virtuais, o Cloudyn pode recolher métricas expandidas, como, por exemplo, da CPU e da memória. Este artigo descreve como ativar o acesso através das APIs do Azure Resource Manager para contas novas e existentes. Descreve também como resolver problemas comuns com contas.

O Cloudyn não consegue aceder à maior parte dos seus dados de subscrição do Azure quando a subscrição está _desativada_. Tem de editar contas _desativadas_ para que o Cloudyn possa aceder às mesmas.

## <a name="required-azure-permissions"></a>Permissões do Azure necessárias

São necessárias permissões específicas para concluir os procedimentos deste artigo. Ambas as seguintes permissões são necessárias para si ou para o administrador do seu inquilino:

- Permissão para registar a aplicação CloudynCollector no seu inquilino do Azure AD.
- A capacidade de atribuir a aplicação a uma função nas suas subscrições do Azure.

Nas suas subscrições do Azure, as contas têm de ter acesso `Microsoft.Authorization/*/Write` para atribuir a aplicação CloudynCollector. Esta ação é concedida através das funções [Proprietário](../role-based-access-control/built-in-roles.md#owner) ou [Administrador de Acesso dos Utilizadores](../role-based-access-control/built-in-roles.md#user-access-administrator).

Se estiver atribuída a função **Contribuidor** à sua conta, não tem permissão adequada para atribuir a aplicação. Verá um erro ao tentar atribuir a aplicação CloudynCollector à sua subscrição do Azure.

### <a name="check-azure-active-directory-permissions"></a>Verificar as permissões do Azure Active Directory

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, selecione **Azure Active Directory**.
3. No Azure Active Directory, selecione **Definições do utilizador**.
4. Verifique a opção **Registos das aplicações**.
    - Se estiver definida para **Sim**, os utilizadores que não forem administradores podem registar aplicações do AD. Esta definição significa que qualquer utilizador no inquilino do Azure AD pode registar uma aplicação.  
    ![selecionar registos das aplicações nas definições de utilizador](./media/activate-subs-accounts/app-register.png)
    - Se a opção **Registos das aplicações** estiver definida para **Não**, apenas os utilizadores administrativos do inquilino podem registar aplicações do Azure Active Directory. O administrador do seu inquilino tem de registar a aplicação CloudynCollector.


## <a name="add-an-account-or-update-a-subscription"></a>Adicionar uma conta ou atualizar uma subscrição

Ao adicionar uma conta ou atualizar uma subscrição, concede ao Cloudyn acesso aos seus dados do Azure.

### <a name="add-a-new-account-subscription"></a>Adicionar uma nova conta (subscrição)

1. No portal do Cloudyn, clique no símbolo de engrenagem na parte superior direita e selecione **Contas da Cloud**.
2. Clique em **Adicionar nova conta** para apresentar a caixa **Adicionar nova conta**. Introduza as informações necessárias.  
    ![Introduza as informações necessárias na caixa de adicionar nova conta](./media/activate-subs-accounts/add-new-account.png)

### <a name="update-a-subscription"></a>Atualizar uma subscrição

1. Se quiser atualizar uma subscrição _desativada_ já existente no Cloudyn, em Gestão de Contas, clique no símbolo de lápis de edição à direita do _GUID de inquilino_ principal. As subscrições são agrupadas num inquilino principal, pelo que deve evitar ativar subscrições individualmente.
    ![Selecione o seu ID de inquilino na caixa de subscrições de detetar novamente](./media/activate-subs-accounts/existing-sub.png)
2. Se for necessário, introduza o ID de Inquilino. Se não souber o ID de Inquilino, siga os passos abaixo para o encontrar:
    1. Inicie sessão no [portal do Azure](https://portal.azure.com).
    2. No portal do Azure, selecione **Azure Active Directory**.
    3. Para obter o ID de inquilino, selecione as **Propriedades** do seu inquilino do Azure AD.
    4. Copie o GUID do ID do Diretório. Este valor é o ID do inquilino.
    Para obter mais informações, veja [Obter o ID de inquilino](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
3. Se for necessário, selecione o ID de Taxa. Se não souber o ID de Taxa, siga os passos abaixo para o encontrar:
    1. No canto superior direito do portal do Azure, clique nas suas informações de utilizador e em **Ver a minha fatura**.
    2. Em **Conta de Faturação**, clique em **Subscrições**.
    3. Em **As minhas subscrições**, selecione a subscrição.
    4. O ID de Taxa é apresentado em **ID de Oferta**. Copie o ID de Oferta para a subscrição.
4. Na caixa Adicionar nova conta (ou Editar Subscrição), clique em **Guardar** (ou **Seguinte**). Será redirecionado para o portal do Azure.
5. Inicie sessão no portal. Clique em **Aceitar** para autorizar o acesso do Recoletor do Cloudyn à sua conta do Azure.

    Será redirecionado para a página de gestão de Contas do Cloudyn e a sua subscrição será atualizada com o Estado de Conta **ativa**. Deverá ser apresentado um símbolo de marca de verificação verde na coluna do Resource Manager.

    Se não for apresentado um símbolo de marca de verificação verde para uma ou mais subscrições, isso significa que não tem permissões para criar a aplicação de leitor (CloudynCollector) para a subscrição. Este processo tem de ser repetido por um utilizador com permissões superiores para a subscrição.

Veja o vídeo [Connecting to Azure Resource Manager with Cloudyn](https://youtu.be/oCIwvfBB6kk) (Ligar ao Azure Resource Manager com o Cloudyn) que explica o processo.

>[!VIDEO https://www.youtube.com/embed/oCIwvfBB6kk?ecver=1]

## <a name="resolve-common-indirect-enterprise-set-up-problems"></a>Resolver problemas comuns de configuração da empresa indireta

Ao utilizar o portal do Cloudyn pela primeira vez, poderão ser apresentadas as seguintes mensagens se for utilizador do Contrato Enterprise ou Fornecedor de Soluções Cloud (CSP):

- *A chave de API especificada não é uma chave de inscrição de nível superior*, apresentada no assistente de **Configuração do Cloudyn**.
- *Inscrição Direta – Não*, apresentada no portal do Contrato Enterprise.
- *Não foram encontrados dados de utilização nos últimos 30 dias. Contacte o seu distribuidor para garantir que a marcação foi ativada para a sua conta do Azure*, apresentada no portal do Cloudyn.

As mensagens anteriores indicam que adquiriu um Contrato Enterprise do Azure através de um revendedor ou CSP. O seu revendedor ou CSP tem de ativar a _marcação_ para a sua conta do Azure, para que possa ver os dados no Cloudyn.

Eis como resolver os problemas:

1. O seu revendedor tem de ativar a _marcação_ para a sua conta. Para obter instruções, consulte o [Guia de Introdução do Cliente Indireto](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).
2. A chave do Contrato Enterprise do Azure para utilizar com o Cloudyn é gerida por si. Para obter instruções, veja [Registar um Contrato Enterprise do Azure e ver dados de custos](https://docs.microsoft.com/azure/cost-management/quick-register-ea).

Para poder gerar a chave de API do Contrato Enterprise do Azure, para configurar o Cloudyn, tem de ativar a API de Faturação do Azure ao seguir as instruções em:

- [Descrição geral de APIs de Relatórios para clientes Enterprise](../billing/billing-enterprise-api.md)
- [API de Relatórios do portal empresarial do Microsoft Azure](https://ea.azure.com/helpdocs/reportingAPI) em **Ativar o acesso a dados para a API**

Também poderá ter de conceder permissões a administradores de departamento, proprietários de conta e administradores empresariais para _ver custos_ com a API de Faturação.

Apenas um administrador de serviços do Azure pode ativar o Cloudyn. As permissões de coadministrador são insuficientes. No entanto, pode contornar o requisito de administrador. Pode pedir que o administrador do Azure Active Directory conceda permissão para autorizar o **CloudynAzureCollector** com um script do PowerShell. O script seguinte concede permissão para registar o **CloudynAzureCollector** do Principal de Serviço do Azure Active Directory.


```powershell
#THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#Tenant - enter your tenant ID or Name
$tenant = "<ReplaceWithYourTenantID>"

#Cloudyn Collector application ID
$appId = "83e638ef-7885-479f-bbe8-9150acccdb3d"

#URL to activate the consent screen
$url = "https://login.windows.net/"+$tenant+"/oauth2/authorize?api-version=1&response_type=code&client_id="+$appId+"&redirect_uri=http%3A%2F%2Flocalhost%3A8080%2FCloudynJava&prompt=consent"

#Choose your browser, the default is Internet Explorer

#Chrome
#[System.Diagnostics.Process]::Start("chrome.exe", "--incognito $url")

#Firefox
#[System.Diagnostics.Process]::Start("firefox.exe","-private-window $url" )

#IExplorer
[System.Diagnostics.Process]::Start("iexplore.exe","$url -private" )

```

## <a name="next-steps"></a>Passos Seguintes

- Se ainda não tiver concluído o primeiro tutorial para o Cloudyn, leia-o em [Rever a utilização e os custos](tutorial-review-usage.md).
