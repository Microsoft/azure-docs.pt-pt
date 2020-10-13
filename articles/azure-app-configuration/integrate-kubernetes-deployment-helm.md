---
title: Integre a configuração da aplicação Azure com a implantação de Kubernetes usando o Helm
description: Aprenda a usar configurações dinâmicas na implementação de Kubernetes com Helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 2b5440ad2bec94d4ef14fa29e723cc91a4fcdf10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766865"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integre-se com a implantação de Kubernetes usando o Helm

O Helm fornece uma forma de definir, instalar e atualizar aplicações em execução em Kubernetes. Um gráfico Helm contém as informações necessárias para criar uma instância de uma aplicação Kubernetes. A configuração é armazenada fora do próprio gráfico, num ficheiro chamado *values.yaml*. 

Durante o processo de libertação, helm funde o gráfico com a configuração adequada para executar a aplicação. Por exemplo, as variáveis definidas em *valores.yaml* podem ser referenciadas como variáveis ambientais dentro dos recipientes de funcionamento. Helm também apoia a criação de Kubernetes Secrets, que pode ser montado como volume de dados ou exposto como variáveis ambientais.

Pode sobrepor-se aos valores armazenados em *values.yaml,* fornecendo ficheiros de configuração adicionais baseados em YAML na linha de comando ao executar o Helm. A Azure App Configuration suporta valores de configuração de exportação para ficheiros YAML. A integração desta capacidade de exportação na sua implementação permite que as suas aplicações Kubernetes aproveitem os valores de configuração armazenados na Configuração da Aplicação.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Utilize valores da Configuração da Aplicação ao implementar uma aplicação para Kubernetes utilizando o Helm.
> * Crie um Segredo de Kubernetes baseado numa referência do Cofre de Chaves na Configuração de Aplicações.

Este tutorial pressupõe a compreensão básica de gerir Kubernetes com Helm. Saiba mais sobre a instalação de aplicações com o Helm no [Serviço Azure Kubernetes.](https://docs.microsoft.com/azure/aks/kubernetes-helm)

## <a name="prerequisites"></a>Pré-requisitos

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- Instalar [O Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) (versão 2.4.0 ou posterior)
- Instalar [o Leme](https://helm.sh/docs/intro/install/) (versão 2.14.0 ou posterior)
- Um aglomerado de Kubernetes.

## <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Configuration Explorer**  >  **Create** para adicionar os seguintes pares de valor-chave:

    | Chave | Valor |
    |---|---|
    | definições.cor | Branco |
    | definições.mensagem | Dados da Configuração da Aplicação Azure |

    Deixe **a etiqueta** e o tipo de **conteúdo** vazios por enquanto.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicione uma referência do Cofre de Chaves à Configuração de Aplicações
1. Inscreva-se no [portal Azure](https://portal.azure.com) e adicione um segredo ao [Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) com o nome **Password** e **valorize a minha palavra-passe**. 
2. Selecione a instância da loja de configuração de aplicação que criou na secção anterior.

3. Selecione **O Explorador de Configuração**.

4. Selecione **+ Criar**  >  **referência de cofre de chave**e, em seguida, especificar os seguintes valores:
    - **Tecla**: Selecione **secrets.password**.
    - **Etiqueta**: Deixe este valor em branco.
    - **Subscrição**, **Grupo de Recursos**e Cofre **chave**: Introduza os valores correspondentes aos do cofre-chave que criou no degrau anterior.
    - **Segredo**: Selecione o segredo chamado **Password** que criou na secção anterior.

## <a name="create-helm-chart"></a>Criar gráfico helm ##
Primeiro, crie um gráfico de helm de amostra com o seguinte comando
```console
helm create mychart
```

Helm cria um novo diretório chamado mychart com a estrutura mostrada abaixo. 

> [!TIP]
> Siga este [guia de gráficos](https://helm.sh/docs/chart_template_guide/getting_started/) para saber mais.

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

Em seguida, atualize a **especificação:modelo:spec:containers** section do ficheiro *deployment.yaml.* O seguinte corte adiciona duas variáveis ambientais ao recipiente. Definirá os seus valores dinamicamente no tempo de implantação.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

O ficheiro *completa de implementação.yaml* após a atualização deve parecer abaixo.

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

Para armazenar dados sensíveis como Segredos de Kubernetes, adicione um ficheiro *secrets.yaml* sob a pasta de modelos.

> [!TIP]
> Saiba mais sobre como usar os [Segredos de Kubernetes.](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

Por fim, atualize o ficheiro *values.yaml* com o seguinte conteúdo para fornecer opcionalmente valores predefinidos das definições e segredos de configuração que referenciaram nos ficheiros *de implementação.yaml* e *secrets.yaml.* Os seus valores reais serão substituídos por configuração retirada da Configuração da Aplicação.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Configuração de passe a partir da configuração da aplicação na instalação helm ##
Em primeiro lugar, descarregue a configuração da Configuração de Aplicação para um ficheiro *myConfig.yaml.* Utilize um filtro de teclas apenas para descarregar as teclas que começam com **as definições.** Se, no seu caso, o filtro de chave não for suficiente para excluir as teclas das referências do Key Vault, poderá utilizar o argumento **--skip-keyvault** para excluí-las. 

> [!TIP]
> Saiba mais sobre o [comando de exportação.](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export) 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Em seguida, baixe segredos para um ficheiro chamado *mySecrets.yaml*. O argumento da linha de comando **--resolve-keyvault** resolve as referências do Cofre de Chaves recuperando os valores reais no Cofre de Chaves. Terá de executar este comando com credenciais que tenham permissões de acesso ao cofre de chaves correspondente.

> [!WARNING]
> Como este ficheiro contém informações sensíveis, mantenha o ficheiro cuidado e limpe-o quando já não for necessário.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Use o argumento **de -f** da atualização do leme para passar nos dois ficheiros de configuração que criou. Eles vão sobrepor-se aos valores de configuração definidos em *values.yaml* com os valores exportados da Configuração de Aplicações.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

Também pode usar o argumento **conjunto para** a atualização do leme para passar valores de chave literal. Usar o argumento **--set** é uma boa maneira de evitar a persistência de dados sensíveis no disco. 

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

Verifique se as configurações e segredos foram definidos com sucesso acedendo ao [Painel de Instrumentos Kubernetes](https://docs.microsoft.com/azure/aks/kubernetes-dashboard). Verá que os valores de **cor** e **mensagem** da Configuração da Aplicação foram povoados nas variáveis ambientais do contentor.

![Quickstart app lançar local](./media/kubernetes-dashboard-env-variables.png)

Um segredo, **palavra-passe,** lojas como referência Key Vault na Configuração de Aplicações também foi adicionado em Kubernetes Secrets. 

![Quickstart app lançar local](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você exportou dados de Configuração de aplicações Azure para serem usados numa implementação de Kubernetes com Helm. Para saber mais sobre como usar a Configuração de Aplicações, continue para as amostras do Azure CLI.

> [!div class="nextstepaction"]
> [CLI do Azure](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
