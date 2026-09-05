# 🏥 Sistema Urologger AI 

O **Urologger AI** é um Progressive Web App (PWA) estático projetado para automatizar e digitalizar o monitoramento do débito urinário hospitalar ou residencial. Através do processamento de imagens de um coletor de urina rígido acoplado a um suporte padronizado, o sistema automatiza a leitura de volume, análise cromática e calcula o débito urinário em tempo real (ml/kg/h).

O sistema roda **100% no lado do cliente (client-side)**, garantindo total privacidade dos dados médicos.

## 🚀 Funcionalidades Principais

*   **Validação Automática de Entrada:** Filtro computacional pré-processamento para garantir a qualidade da imagem:
    *   *Nitidez:* Teste de desfoque por Variância de Laplaciano.
    *   *Orientação:* Detecção de linhas da escala (rejeita inclinações > 3°).
    *   *Luminosidade:* Análise do histograma para alertar subexposição.
*   **Motor de Processamento (Triple Validation):**
    *   *Segmentação de Nível:* Identificação da interface ar-líquido via gradiente de contraste.
    *   *Contagem de Traços:* Identificação física das marcações do coletor.
    *   *OCR (Tesseract.js):* Validação dos números impressos na escala.
    *   *Geometria Fixa:* Proporção de pixels por regra de 3 com base na rigidez do recipiente.
*   **Cálculo Dinâmico de Débito:** Integração baseada no peso do paciente, intervalo de tempo desde o último esvaziamento e volume acumulado.
*   **Privacidade e Segurança:** Persistência exclusiva no `localStorage` do navegador do dispositivo.
*   **Relatórios Profissionais:** Exportação instantânea de histórico em `CSV` e relatórios clínicos em `PDF` com gráficos de tendência.

## 📦 Estrutura do Repositório

```text
├── index.html          # Interface do usuário (Tailwind) e lógica JS principal
├── manifest.json       # Configuração para instalação como PWA nativo
├── service-worker.js   # Script de cache estático para operação offline
├── LICENSE             # Licença de código aberto (MIT)
└── README.md           # Este manual de documentação
```

## 🛠️ Instalação e Execução Local

Como o Urologger AI é um sistema estático, ele **não exige servidores nem dependências de backend**.

1. **Clone o repositório:**
   ```bash
   git clone https://github.com
   ```
2. **Navegue até a pasta:**
   ```bash
   cd urologger-ai
   ```
3. **Execute um servidor local** para testar as capacidades de Service Worker e PWA (necessário protocolo `http://localhost` ou `https`):
   ```bash
   # Utilizando Python 3
   python -m http.server 8080
   ```
4. Abra o seu navegador em `http://localhost:8080`.

## 🌐 Publicação no GitHub Pages

1. Crie um repositório público no GitHub chamado `urologger-ai`.
2. Envie todos os arquivos da raiz para o repositório.
3. Acesse **Settings** > **Pages** no seu repositório do GitHub.
4. Em **Build and deployment**, selecione a branch `main` (ou `master`) e a pasta `/ (root)`.
5. Clique em **Save**. Em poucos minutos seu PWA estará disponível em `https://github.io`.

## 🔒 Declaração de Limitação de Responsabilidade (Disclaimer)
Este software é uma prova de conceito de engenharia biomédica e processamento de imagens. Embora utilize métodos rigorosos de validação tripla, não substitui o julgamento clínico de profissionais de saúde qualificados. Os desenvolvedores não se responsabilizam por decisões médicas tomadas com base nas leituras automatizadas.
