//Algoritmos_Lab08
//Desarrolo del alb08 de Algortimos

public class AVLTree<E extends Comparable<E>> extends BSTree<E> {
    class NodeAVL extends Node {
        protected int bf; // factor de equilibrio

        // Constructor
        public NodeAVL(E data) {
            super(data);
            this.bf = 0; // El factor de equilibrio inicial es 0
        }

        // Redefinir el método toString
        @Override
        public String toString() {
            return "Data: " + this.data + ", BF: " + this.bf;
        }
    }

    private boolean height; // Indicador de cambio de altura

    //Insertar método para ser utilizado públicamente 
    public void insert(E x) throws ItemDuplicated {
        this.height = false;
        this.root = insert(x, (NodeAVL) this.root);
    }

    // Método de inserción para uso interno.
    protected Node insert(E x, NodeAVL node) throws ItemDuplicated {
        NodeAVL fat = node;
        if (node == null) {
            this.height = true;
            fat = new NodeAVL(x);
        } else {
            int resC = node.data.compareTo(x);
            if (resC == 0) {
                throw new ItemDuplicated(x + " ya se encuentra en el arbol...");
            }
            if (resC < 0) {
                fat.right = insert(x, (NodeAVL) node.right);
                if (this.height) {
                    switch (fat.bf) {
                        case -1:
                            fat.bf = 0;
                            this.height = false;
                            break;
                        case 0:
                            fat.bf = 1;
                            this.height = true;
                            break;
                        case 1:
                            // bf = 2, necesita equilibrio
                            fat = balanceToLeft(fat);
                            this.height = false;
                            break;
                    }
                }
            } else {
                // Insertar en el lado izquierdo
                fat.left = insert(x, (NodeAVL) node.left);
                if (this.height) {
                    switch (fat.bf) {
                        case 1:
                            fat.bf = 0;
                            this.height = false;
                            break;
                        case 0:
                            fat.bf = -1;
                            this.height = true;
                            break;
                        case -1:
                            // bf = -2, necesita equilibrio
                            fat = balanceToRight(fat);
                            this.height = false;
                            break;
                    }
                }
            }
        }
        return fat;
    }

    // Metodo para equilibrar hacia la izquierda.
    private NodeAVL balanceToLeft(NodeAVL node) {
        NodeAVL hijo = (NodeAVL) node.right;
        switch (hijo.bf) {
            case 1:
                node.bf = 0;
                hijo.bf = 0;
                node = rotateSL(node);
                break;
            case -1:
                NodeAVL nieto = (NodeAVL) hijo.left;
                switch (nieto.bf) {
                    case -1:
                        node.bf = 0;
                        hijo.bf = 1;
                        break;
                    case 0:
                        node.bf = 0;
                        hijo.bf = 0;
                        break;
                    case 1:
                        node.bf = 1;
                        hijo.bf = 0;
                        break;
                }
                nieto.bf = 0;
                node.right = rotateSR(hijo);
                node = rotateSL(node);
                break;
        }
        return node;
    }

    // Metodo para equilibrar hacia la derecha.
    private NodeAVL balanceToRight(NodeAVL node) {
        NodeAVL hijo = (NodeAVL) node.left;
        switch (hijo.bf) {
            case -1:
                node.bf = 0;
                hijo.bf = 0;
                node = rotateSR(node);
                break;
            case 1:
                NodeAVL nieto = (NodeAVL) hijo.right;
                switch (nieto.bf) {
                    case 1:
                        node.bf = 0;
                        hijo.bf = -1;
                        break;
                    case 0:
                        node.bf = 0;
                        hijo.bf = 0;
                        break;
                    case -1:
                        node.bf = -1;
                        hijo.bf = 0;
                        break;
                }
                nieto.bf = 0;
                node.left = rotateSL(hijo);
                node = rotateSR(node);
                break;
        }
        return node;
    }

    // Marcador de posicion para el metodo turnSL (rotacion unica a la izquierda)
    private NodeAVL rotateSL(NodeAVL node) {
        NodeAVL newRoot = (NodeAVL) node.right;
        node.right = newRoot.left;
        newRoot.left = node;
        return newRoot;
    }

    // Marcador de posicion para el metodo turnSR (rotacion unica a la derecha)
    private NodeAVL rotateSR(NodeAVL node) {
        NodeAVL newRoot = (NodeAVL) node.left;
        node.left = newRoot.right;
        newRoot.right = node;
        return newRoot;
    }

    // Otros métodos e implementación de AVLTree
}
