enum Color { RED, BLACK };

struct Node{
        int key;
        Color color;
        Node* left;
        Node* right;
        Node* parent;
        Node(int dt): key(dt) ,color(RED), left(nullptr) , right(nullptr) , parent(nullptr){}
};

class set{
private:
    Node* root;
    
    void makerootblack(){
        if(root != nullptr){
            root->color = BLACK;
        }
    }
    
    void leftrotate(Node* x){
        Node* y = x->right;
        x->right = y->left;
        if(y->left != nullptr){ y->left->parent = x;}
        y->parent = x->parent;
        if(x->parent == nullptr){
            root = y;
        }else if(x->parent->left == x){
            x->parent->left = y;
        }else{
            x->parent->right = y;
        }
        y->left = x;
        x->parent = y;
    }
    
    void rightrotate(Node* x){
        Node* y = x->left;
        x->left = y->right;
        if(y->right != nullptr){ y->right->parent = x;}
        y->parent = x->parent;
        if(x->parent == nullptr){
            root = y;
        }else if(x->parent->left == x){
            x->parent->left = y;
        }else{
            x->parent->right = y;
        }
        y->right = x;
        x->parent = y;
        }
    
    void insertFixup(Node* x){
        while(x->parent != nullptr && x->parent->color == RED){
            if(x->parent == x->parent->parent->left){
                Node* uncle = x->parent->parent->right;
                
                if(uncle != nullptr && uncle->color == RED){
                    x->parent->color = BLACK;
                    uncle->color = BLACK;
                    x->parent->parent->color = RED;
                    x = x->parent->parent;
                }else{
                    if(x == x->parent->right){
                        x = x->parent;
                        leftrotate(x);
                    }
                    x->parent->color = BLACK;
                    x->parent->parent->color = RED;
                    rightrotate(x->parent->parent);
                }
            }else{
                Node* uncle = x->parent->parent->left;
                
                if (uncle != nullptr && uncle->color == RED){
                    x->parent->color = BLACK;
                    uncle->color = BLACK;
                    x->parent->parent->color = RED;
                    x = x->parent->parent;
                } else {
                    if (x == x->parent->left) {
                        x = x->parent;
                        rightrotate(x);
                    }
                    x->parent->color = BLACK;
                    x->parent->parent->color = RED;
                    leftrotate(x->parent->parent);
                }
            }
        }
        root->color = BLACK;
    }
public:
    set() : root(nullptr) {}
    set(int x) : root(new Node(x)) {makerootblack();}
   
    void insert(int val){
        Node* newNode = new Node(val);
        if(root == nullptr){
            root = newNode;
            return;
        }
       
        Node* tmp = root;
        Node* parent = nullptr;
        while(tmp != nullptr){
            parent = tmp;
            if(val < tmp->key){
                tmp = tmp->left;
            }else if(val > tmp->key){
                tmp = tmp->right;
            }else{
                delete newNode;
                return;
            }
        }
       
        newNode->parent = parent;
        if(val < parent->key){
            parent->left = newNode;
        }else if(val > parent->key){
            parent->right = newNode;
        }
        insertFixup(newNode);
    }
   
    bool find(int val){
        Node* tmp = root;
        while(tmp != nullptr){
            if(val < tmp->key){
                tmp = tmp->left;
            }else if(val > tmp->key){
                tmp = tmp->right;
            }else if(val == tmp->key){
                return true;
            }
        }
        return false;
    }
   
    void delete_(int val){
        Node* tmp = root;
        while(tmp != nullptr && tmp->key != val){
            if(tmp->key > val){
                tmp = tmp->left;
            }else if(tmp->key < val){
                tmp = tmp->right;
            }else{
                break;
            }
        }
        if(tmp == nullptr){ return; }
        if(tmp->left == nullptr || tmp->right == nullptr){
            Node* child = (tmp->left != nullptr) ? tmp->left : tmp->right;
            
            if(tmp->parent == nullptr){
                root = child;
            }else if(tmp == tmp->parent->left){
                tmp->parent->left = child;
            }else{
                tmp->parent->right = child;
            }
            
            if(child != nullptr){
                child->parent = tmp->parent;
            }
            
            delete tmp;
        }else{
            Node* keeper = tmp->right;
            while(keeper->left != nullptr){
                keeper = keeper->left;
            }
            
            tmp->key = keeper->key;
            
            Node* child = keeper->right;
            
            if(keeper->parent == tmp){
                keeper->right = child;
            }else{
                keeper->parent->left = child;
            }
            
            if(child != nullptr){
                child->parent = keeper->parent;
            }
            
            delete keeper;
        }
    }
   
    int findminNode(){
        if(empty()){return 0;}
        
        Node* tmp = root;
        while(tmp->left != nullptr){
            tmp = tmp->left;
        }
        return tmp->key;
    }
    void inorder(){
        inorder(root);
        std::cout<<std::endl;
    }
    void inorder(Node* n){
       
        if(n!=nullptr){
            inorder(n->left);
            std::cout<<n->key<<" ";
            inorder(n->right);
           
        }
    }
   
    void clear(){
        Node* tmp = root;
        clear(tmp);
    }
   
    void clear(Node* N){
        if(N != nullptr){
            clear(N->left);
            clear(N->right);
            delete N;
        }
    }
    
    bool empty(){return root == nullptr;}
    ~set(){clear();}
};
