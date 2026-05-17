const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json());

// Banco de dados temporário (em memória)
let livros = [
    { id: 1, titulo: "O Senhor dos Anéis", autor: "J.R.R. Tolkien", categoriaId: 1 },
    { id: 2, titulo: "Neuromancer", autor: "William Gibson", categoriaId: 2 }
];

let categorias = [
    { id: 1, nome: "Fantasia" },
    { id: 2, nome: "Ficção Científica" }
];

// ==========================================
// ROTA: /autor (Negociação de Conteúdo)
// ==========================================
app.get('/autor', (req, res) => {
    const acceptHeader = req.headers['accept'];

    const dadosAutor = {
        nome: "Seu Nome Aqui",
        github: "https://github.com/seu-usuario",
        descricao: "Estudante de Desenvolvimento Web"
    };

    if (acceptHeader && acceptHeader.includes('application/json')) {
        return res.json(dadosAutor);
    } else {
        res.setHeader('Content-Type', 'text/html');
        return res.send(`
            <h1>Autor do Projeto</h1>
            <p><strong>Nome:</strong> ${dadosAutor.nome}</p>
            <p><strong>GitHub:</strong> <a href="${dadosAutor.github}">${dadosAutor.github}</a></p>
        `);
    }
});

// ==========================================
// ENDPOINTS RESTful: Tema (Livros)
// ==========================================

// GET - Listar todos os livros
app.get('/api/livros', (req, res) => {
    res.json(livros);
});

// POST - Criar um novo livro
app.post('/api/livros', (req, res) => {
    const { titulo, autor, categoriaId } = req.body;
    const novoLivro = {
        id: livros.length + 1,
        titulo,
        autor,
        categoriaId: Number(categoriaId)
    };
    livros.push(novoLivro);
    res.status(201).json(novoLivro);
});

// PUT - Atualizar um livro existente
app.put('/api/livros/:id', (req, res) => {
    const id = Number(req.params.id);
    const { titulo, autor, categoriaId } = req.body;
    
    const index = livros.findIndex(l => l.id === id);
    if (index === -1) return res.status(404).json({ erro: "Livro não encontrado" });

    livros[index] = { id, titulo, autor, categoriaId: Number(categoriaId) };
    res.json(livros[index]);
});

// DELETE - Deletar um livro
app.delete('/api/livros/:id', (req, res) => {
    const id = Number(req.params.id);
    const index = livros.findIndex(l => l.id === id);
    
    if (index === -1) return res.status(404).json({ erro: "Livro não encontrado" });

    livros.splice(index, 1);
    res.status(204).send(); // 204 No Content
});

// ==========================================
// ENDPOINTS RESTful: Categorias
// ==========================================

// GET - Listar todas as categorias
app.get('/api/categorias', (req, res) => {
    res.json(categorias);
});

// POST - Criar uma categoria
app.post('/api/categorias', (req, res) => {
    const { nome } = req.body;
    const novaCategoria = { id: categorias.length + 1, nome };
    categorias.push(novaCategoria);
    res.status(201).json(novaCategoria);
});

// PUT - Atualizar uma categoria
app.put('/api/categorias/:id', (req, res) => {
    const id = Number(req.params.id);
    const { nome } = req.body;

    const index = categorias.findIndex(c => c.id === id);
    if (index === -1) return res.status(404).json({ erro: "Categoria não encontrada" });

    categorias[index] = { id, nome };
    res.json(categorias[index]);
});

// DELETE - Deletar uma categoria
app.delete('/api/categorias/:id', (req, res) => {
    const id = Number(req.params.id);
    const index = categorias.findIndex(c => c.id === id);

    if (index === -1) return res.status(404).json({ erro: "Categoria não encontrada" });

    categorias.splice(index, 1);
    res.status(204).send();
});

app.listen(PORT, () => {
    console.log(`Servidor rodando em http://localhost:${PORT}`);
});
