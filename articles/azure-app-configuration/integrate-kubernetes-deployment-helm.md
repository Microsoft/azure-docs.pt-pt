---
title: Integre a configuração da app Azure com a implantação de Kubernetes usando o Helm
description: Aprenda a usar configurações dinâmicas na implementação de Kubernetes com helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 2aebccdf18aaba345beb344a8b6fc3b37754a4a1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793618"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integrar com a Implantação kubernetes usando helm

O Helm fornece uma forma de definir, instalar e atualizar aplicações em execução em Kubernetes. Um gráfico helm contém as informações necessárias para criar uma instância de uma aplicação Kubernetes. A configuração é armazenada fora do gráfico em si, num ficheiro chamado *values.yaml*. 

Durante o processo de lançamento, helm funde o gráfico com a configuração adequada para executar a aplicação. Por exemplo, variáveis definidas em *valores.yaml* podem ser referenciadas como variáveis ambientais dentro dos recipientes de corrida. Helm também apoia a criação de Kubernetes Secrets, que podem ser montados como volumes de dados ou expostos como variáveis ambientais.

Pode anular os valores armazenados em *valores.yaml* fornecendo ficheiros de configuração baseados em YAML adicionais na linha de comando ao executar o Helm. A Configuração de Aplicações Azure suporta a exportação de valores de configuração para ficheiros YAML. Integrar esta capacidade de exportação na sua implementação permite que as suas aplicações Kubernetes aproveitem os valores de configuração armazenados na Configuração da App.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilize valores a partir da Configuração da Aplicação ao implementar uma aplicação para kubernetes utilizando o Helm.
> * Crie um Segredo Kubernetes baseado numa referência chave vault na configuração da aplicação.

Este tutorial assume a compreensão básica de gerir Kubernetes com Helm. Saiba mais sobre a instalação de aplicações com helm no [Serviço Azure Kubernetes.](https://docs.microsoft.com/azure/aks/kubernetes-helm)

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Instalar [o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (versão 2.4.0 ou mais tarde)
- Instalar [o Leme](https://helm.sh/docs/intro/install/) (versão 2.14.0 ou mais tarde)
- Um aglomerado de Kubernetes.

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicações

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Configuração Explorer** > **Criar** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | configurações.color | Branco |
    | definições.mensagem | Dados da Configuração de Aplicações Azure |

    Deixe o **rótulo** e o **tipo de conteúdo** vazios por enquanto.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicione uma referência chave vault à configuração da aplicação
1. Inscreva-se no [portal Azure](https://portal.azure.com) e adicione um segredo ao [Cofre chave](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) com o nome **Password** e valorize a **minha Palavra-passe**. 
2. Selecione a instância da loja de configuração de aplicações que criou na secção anterior.

3. Selecione Explorador de **Configuração**.

4. Selecione **+ Criar** > **referência de cofre chave,** e depois especificar os seguintes valores:
    - **Chave**: Selecione **segredos.password**.
    - **Etiqueta**: Deixe este valor em branco.
    - **Subscrição,** **Grupo de Recursos**e Cofre **chave**: Introduza os valores correspondentes aos do cofre chave que criou em passo anterior.
    - **Segredo**: Selecione o segredo denominado **Palavra-passe** que criou na secção anterior.

## <a name="create-helm-chart"></a>Criar gráfico helm ##
Primeiro, crie uma amostra de gráfico helm com o seguinte comando
```console
helm create mychart
```

Helm cria um novo diretório chamado mychart com a estrutura mostrada abaixo. 

> [!TIP]
> Siga este guia de [gráficos](https://helm.sh/docs/chart_template_guide/getting_started/) para saber mais.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Em seguida, atualize a **secção spec:template:spec:containers** do ficheiro *deployment.yaml.* O seguinte corte adiciona duas variáveis ambientais ao recipiente. Vai definir os valores deles de forma dinâmica no momento da implantação.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

O ficheiro de *implementação completo.yaml* após a atualização deve parecer abaixo.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Para armazenar dados sensíveis como Kubernetes Secrets, adicione um ficheiro *secrets.yaml* sob a pasta de modelos.

> [!TIP]
> Saiba mais sobre como usar os [Segredos kubernetes.](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password }}
```

Por fim, atualize o ficheiro *values.yaml* com o seguinte conteúdo para fornecer opcionalmente valores predefinidos das definições de configuração e segredos que se referem no *ficheiro deployment.yaml* e *secrets.yaml.* Os seus valores reais serão substituídos por configuração retirada da Configuração da App.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Configuração de passe da configuração da app na instalação Helm ##
Primeiro, descarregue a configuração da Configuração da App para um ficheiro *myConfig.yaml.* Utilize um filtro de teclas para apenas descarregar as teclas que começam com **as definições. .**. Se, no seu caso, o filtro da chave não for suficiente para excluir as teclas das referências do Cofre Chave, poderá utilizar o argumento **--salt-keyvault** para as excluir. 

> [!TIP]
> Saiba mais sobre o [comando de exportação.](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export) 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Em seguida, descarregue segredos para um ficheiro chamado *mySecrets.yaml*. O **argumento** da linha de comando resolve as referências do Cofre chave recuperando os valores reais no Cofre chave. Terá de executar este comando com credenciais que tenham permissões de acesso ao cofre chave correspondente.

> [!WARNING]
> Como este ficheiro contém informações sensíveis, mantenha o ficheiro com cuidado e limpe-o quando já não for necessário.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Use o argumento **-f** da atualização do leme para passar nos dois ficheiros de configuração que criou. Eles vão sobrepor-se aos valores de configuração definidos em *valores.yaml* com os valores exportados da Configuração da App.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Também pode usar o argumento **-set** para a atualização do leme para passar valores-chave literais. Usar o argumento **--set** é uma boa maneira de evitar que persistam dados sensíveis ao disco. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Verifique se as configurações e segredos foram definidos com sucesso acedendo ao [Painel de Instrumentos Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard). Verá que os valores de **cor** e **mensagem** da Configuração da App foram povoados nas variáveis ambientais do recipiente.

![Lançamento de app Quickstart local](./media/kubernetes-dashboard-env-variables.png)

Um segredo, **palavra-passe,** lojas como referência chave vault na configuração de aplicativos também foi adicionado em Kubernetes Secrets. 

![Lançamento de app Quickstart local](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, exportou dados de configuração de aplicações do Azure para serem utilizados numa implementação da Kubernetes com a Helm. Para saber mais sobre como utilizar a Configuração da App, continue com as amostras do Azure CLI.

> [!div class="nextstepaction"]
> [CLI do Azure](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
