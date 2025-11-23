# ⚙️ Guia Completo de Configuração - Ninja Low Price

Este guia detalha passo a passo como configurar e implantar o agente **Ninja Low Price** no Azure AI Foundry.

---

## 📋 Pré-requisitos

Antes de começar, você precisará de:

- ✅ Conta Microsoft Azure (gratuita ou paga)
- ✅ Acesso ao [Azure AI Foundry](https://ai.azure.com/)
- ✅ Conta Google (para Google Custom Search API)
- ✅ Conhecimento básico de APIs REST

---

## 🔑 Passo 1: Configurar Google Custom Search API

### 1.1. Criar Programmable Search Engine

1. Acesse [Google Programmable Search Engine](https://programmablesearchengine.google.com/)
2. Clique em **"Get Started"** ou **"Adicionar"**
3. Configure:
   - **Nome do mecanismo:** "Comparador de Preços Brasil"
   - **O que pesquisar:** Selecione "Pesquisar a Web inteira"
   - **Idioma:** Português
   - **Região:** Brasil
4. Clique em **"Criar"**
5. Anote o **Search Engine ID (cx)** - será algo como `f1a79432ac7054753`

### 1.2. Obter API Key do Google

1. Acesse [Google Cloud Console](https://console.cloud.google.com/)
2. Crie um novo projeto ou selecione um existente
3. Vá em **APIs & Services** → **Library**
4. Procure por **"Custom Search API"**
5. Clique em **"Enable"** (Ativar)
6. Vá em **APIs & Services** → **Credentials**
7. Clique em **"Create Credentials"** → **"API Key"**
8. Copie e guarde sua **API Key** - será algo como `AIzaSyAcoQc10ZlCNYc-p0tH5d8gum1HULIaCVI`

⚠️ **IMPORTANTE:** Mantenha sua API Key em segurança!

---

## 🚀 Passo 2: Criar Agente no Azure AI Foundry

### 2.1. Criar Projeto

1. Acesse [Azure AI Foundry](https://ai.azure.com/)
2. Faça login com sua conta Microsoft
3. Clique em **"Novo projeto"** ou **"+ New project"**
4. Preencha:
   - **Nome do projeto:** `new-low-price`
   - **Resource Group:** Crie um novo ou use existente
   - **Região:** Brazil South (ou mais próxima)
5. Clique em **"Criar"**

### 2.2. Criar Agente

1. No menu lateral, vá em **"Agents"** (Agentes)
2. Clique em **"+ Novo agente"** ou **"+ New agent"**
3. Configure:
   - **Nome do agente:** `ninja-low-price`
   - **Modelo:** Selecione **gpt-4.1-nano** (ou gpt-4o-mini para custo reduzido)
   - **Versão:** `2025-04-14` (mais recente disponível)
4. Clique em **"Criar"**

---

## 📝 Passo 3: Configurar Instruções do Agente

1. Na tela do agente, vá na aba **"Instruções"** ou **"Instructions"**
2. Copie e cole o seguinte texto:
   
O agente de IA deve receber o nome de um produto solicitado pelo usuário e realizar automaticamente uma busca de preços em cinco lojas diferentes. Ele deve pesquisar o produto em lojas como Amazon, Mercado Livre, Magalu, Americanas, Casas Bahia, Submarino, Shopee ou outras lojas relevantes. Para cada loja, o agente deve coletar o preço base do produto, identificar se existe frete grátis ou o valor do frete quando disponível, e também obter o link ou identificação da oferta. Em seguida, o agente deve calcular o preço total somando o preço base ao custo do frete, considerando frete grátis como frete igual a zero.

Depois de obter os resultados, o agente deve organizar todas as informações em uma tabela de comparação contendo: nome da loja, preço base, valor do frete, preço total e link da oferta. A tabela deve sempre conter cinco lojas — exceto nos casos em que o produto realmente não for encontrado em todas elas. Se alguma loja não apresentar informações completas de frete, o agente deve indicar "frete não informado".

Com base na tabela, o agente deve identificar automaticamente qual loja oferece o menor preço total e destacar essa opção ao final da resposta, explicando o motivo, por exemplo: frete grátis, menor preço base ou melhor valor final. O agente só deve recomendar a loja após exibir a tabela de comparação completa. Caso o produto não seja encontrado em nenhuma loja, ele deve informar ao usuário que o produto não foi localizado e sugerir que o usuário forneça mais detalhes.

A resposta final do agente deve sempre seguir esta ordem: mostrar o nome do produto pesquisado, apresentar a tabela de comparação das cinco lojas, analisar os resultados e finalmente indicar qual loja possui o melhor preço total. Esse é o comportamento padrão que o agente deve seguir em todas as consultas realizadas.

3. Clique em **"Salvar"** ou **"Save"**

---

## 🔧 Passo 4: Configurar OpenAPI Tool

### 4.1. Preparar arquivo OpenAPI

1. Baixe o arquivo [openapi-spec.json](../openapi-spec.json) deste repositório
2. Abra o arquivo em um editor de texto
3. **SUBSTITUA** os valores das chaves:
   - Linha com `"enum": ["AIzaSy..."]` → Coloque sua **API Key**
   - Linha com `"enum": ["f1a79..."]` → Coloque seu **Search Engine ID (cx)**

### 4.2. Importar Tool no Azure

1. Na tela do agente, vá na aba **"Ações"** ou **"Actions"**
2. Clique em **"+ Adicionar"** ou **"+ Add action"**
3. Selecione **"OpenAPI"**
4. Cole todo o conteúdo do arquivo `openapi-spec.json` (já com suas credenciais)
5. Nomeie a ação como: `apibusca`
6. Clique em **"Salvar"** ou **"Save"**

---

## ✅ Passo 5: Testar o Agente

### 5.1. Teste Básico

1. Na aba **"Nova conversa"** ou **"New conversation"**
2. Digite:
Qual o preço de um notebook Dell?
3. Aguarde a resposta do agente
4. Verifique se:
- ✅ Tabela com lojas foi gerada
- ✅ Preços estão sendo exibidos
- ✅ Recomendação de melhor oferta aparece

### 5.2. Teste de Erro

1. Digite algo muito genérico:
   2. O agente deve pedir mais detalhes

### 5.3. Teste com Produto Específico

1. Digite:
preciso pesquisar uma caixa de som da alexa echo show
2. Verifique se os resultados são relevantes

---

## 🐛 Resolução de Problemas

### Erro: "API Key inválida"
- ✅ Verifique se a API Key foi copiada corretamente
- ✅ Confirme se a Custom Search API está ativada no Google Cloud Console
- ✅ Aguarde até 5 minutos após ativar a API

### Erro: "Nenhum resultado encontrado"
- ✅ Verifique se o Search Engine ID (cx) está correto
- ✅ Certifique-se de que o mecanismo de busca está configurado para "Pesquisar a Web inteira"
- ✅ Teste a API diretamente no navegador:
https://www.googleapis.com/customsearch/v1?key=SUA_KEY&cx=SEU_CX&q=notebook+dell

### Agente não chama a tool
- ✅ Verifique se as instruções foram salvas
- ✅ Confirme se a action foi adicionada e está ativa
- ✅ Reinicie a conversa (clique em "Nova conversa")

---

## 🎉 Próximos Passos

Após configurar com sucesso:

1. ✅ Faça mais testes com diferentes produtos
2. ✅ Tire prints das respostas para documentação
3. ✅ Compartilhe o link do seu repositório GitHub
4. ✅ Participe do Azure Frontier Girls AI Challenge!

---

## 📚 Recursos Adicionais

- [Documentação Azure AI Agents](https://learn.microsoft.com/azure/ai-services/agents/)
- [Google Custom Search JSON API](https://developers.google.com/custom-search/v1/overview)
- [OpenAPI Specification 3.0](https://swagger.io/specification/)

---

**Dúvidas?** Abra uma issue no repositório ou consulte a comunidade Azure!



