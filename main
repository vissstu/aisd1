#include <iostream>
#include <chrono>
#include <random>
#include <vector>
using namespace std;
class BinarySearchTree {
private:
    struct Node {
        int key;
        Node* left;
        Node* right;

        Node(int value) : key(value), left(nullptr), right(nullptr) {}
    };

    Node* root;

    void deleteTree(Node* node) {
        if (!node) return;
        deleteTree(node->left);
        deleteTree(node->right);
        delete node;
    }

    Node* insertNode(Node* node, int key, bool& inserted) {
        if (!node) {
            inserted = true;
            return new Node(key);
        }
        if (key < node->key)
            node->left = insertNode(node->left, key, inserted);
        else if (key > node->key)
            node->right = insertNode(node->right, key, inserted);
        return node;
    }

    bool containsNode(Node* node, int key) const {
        if (!node) return false;
        if (node->key == key) return true;
        if (key < node->key) return containsNode(node->left, key);
        return containsNode(node->right, key);
    }

    Node* eraseNode(Node* node, int key, bool& erased) {
        if (!node) return nullptr;
        if (key < node->key)
            node->left = eraseNode(node->left, key, erased);
        else if (key > node->key)
            node->right = eraseNode(node->right, key, erased);
        else {
            erased = true;
            if (!node->left) {
                Node* temp = node->right;
                delete node;
                return temp;
            }
            if (!node->right) {
                Node* temp = node->left;
                delete node;
                return temp;
            }

            // Найти минимальный элемент в правом поддереве
            Node* minNode = node->right;
            while (minNode && minNode->left)
                minNode = minNode->left;
            node->key = minNode->key;
            node->right = eraseNode(node->right, minNode->key, erased);
        }
        return node;
    }

    void printNode(Node* node) const {
        if (!node) return;
        printNode(node->left);
        std::cout << node->key << " ";
        printNode(node->right);
    }

    Node* copySubtree(Node* node) {
        if (!node) return nullptr;
        return new Node(*node);
    }

    std::vector<int> tree2vector(Node* root, std::vector<int>& result) const{
        if (root == nullptr) return result;
        tree2vector(root->left, result);
        result.push_back(root->key);
        tree2vector(root->right, result);
        return result;
    }

public:
    BinarySearchTree() : root(nullptr) {}

    // конструктор копирования
    BinarySearchTree(const BinarySearchTree& other) : root(nullptr) {
        if (other.root) {
            root = new Node(*other.root);
            if (root) {
                root->left = copySubtree(other.root->left);
                root->right = copySubtree(other.root->right);
            }
        }
    }

    // деструктор
    ~BinarySearchTree() {
        deleteTree(root);
    }

    // оператор присваивания
    BinarySearchTree& operator=(const BinarySearchTree& other) {
        if (this != &other) {
            deleteTree(root);
            root = new Node(*other.root);
        }
        return *this;
    }

    void print() const {
        printNode(root);
        std::cout << std::endl;
    }

    bool insert(int key) {
        bool inserted = false;
        root = insertNode(root, key, inserted);
        return inserted;
    }

    // проверка наличия элемента
    bool contains(int key) const {
        return containsNode(root, key);
    }

    bool erase(int key) {
        bool erased = false;
        root = eraseNode(root, key, erased);
        return erased;
    }

    std::vector<int> tree2vector_() const{
        std::vector<int> result;
        return tree2vector(root, result);
    }
};

bool isSubsetOrSuperset(const BinarySearchTree& tree1, const BinarySearchTree& tree2) {
    auto vec1 = tree1.tree2vector_();
    auto vec2 = tree2.tree2vector_();

    size_t len1 = vec1.size();
    size_t len2 = vec2.size();

    bool isSubset = true;
    for (size_t i = 0; i < len1; ++i) {
        if (std::binary_search(vec2.begin(), vec2.end(), vec1[i]) == false) {
            isSubset = false;
            break;
        }
    }

    bool isSuperset = true;
    for (size_t i = 0; i < len2; ++i) {
        if (std::binary_search(vec1.begin(), vec1.end(), vec2[i]) == false) {
            isSuperset = false;
            break;
        }
    }

    return isSubset || isSuperset;
}


// приват метод tree2vector
// public method вызывающий tree2vec
// в мэйне вызывать tree2vec и уже работать с векторами

//Задание по варианту 2
//bool isSubsetOrSuperset(const BinarySearchTree root, const BinarySearchTree root1){
//    bool isSubset = true;
//    bool isSuperset = true;
//   /* root.checkSubset(root.get_root(), root1, isSubset);
//    root.checkSuperset(root.get_root(), root1, isSuperset);*/
//    std:vector<int> vec = root1.tree2vector_();
//    return isSubset || isSuperset;
//}

// Тестики на время
void runTests() {
    std::random_device rd;
    std::mt19937 gen(rd());

    const int sizes[] = { 1000, 10000, 100000 };
    const int fillAttempts = 100;//попытки заполнения
    const int operationAttempts = 1000;

    for (int size : sizes) {
        std::cout << "\nSize: " << size << std::endl;
        
        double bstFillTime = 0;
        double vectorFillTime = 0;

        //Тест по заполнению
        for (int i = 0; i < fillAttempts; i++) {
            BinarySearchTree bst;
            std::vector<int> vec;

            std::uniform_int_distribution<> dis(0, size * 10);

            auto start = std::chrono::high_resolution_clock::now();
            for (int j = 0; j < size; j++)
                bst.insert(dis(gen));
            auto end = std::chrono::high_resolution_clock::now();
            bstFillTime += std::chrono::duration<double, std::micro>(end - start).count();

            start = std::chrono::high_resolution_clock::now();
            for (int j = 0; j < size; j++)
                vec.push_back(dis(gen));
            end = std::chrono::high_resolution_clock::now();
            vectorFillTime += std::chrono::duration<double, std::micro>(end - start).count();
        }

        // Тесты поиска, вставки и удаления
        BinarySearchTree bst;
        std::vector<int> vec;
        std::uniform_int_distribution<> dis(0, size * 10);

        for (int i = 0; i < size; i++) {
            int val = dis(gen);
            bst.insert(val);
            vec.push_back(val);
        }

        double bstSearchTime = 0, vectorSearchTime = 0;
        double bstInsertTime = 0, vectorInsertTime = 0;
        double bstEraseTime = 0;

        for (int i = 0; i < operationAttempts; i++) {
            int val = dis(gen);

            auto start = std::chrono::high_resolution_clock::now();
            bst.contains(val);
            auto end = std::chrono::high_resolution_clock::now();
            bstSearchTime += std::chrono::duration<double, std::micro>(end - start).count();

            start = std::chrono::high_resolution_clock::now();
            std::find(vec.begin(), vec.end(), val);
            end = std::chrono::high_resolution_clock::now();
            vectorSearchTime += std::chrono::duration<double, std::micro>(end - start).count();

            start = std::chrono::high_resolution_clock::now();
            bst.insert(val);
            end = std::chrono::high_resolution_clock::now();
            bstInsertTime += std::chrono::duration<double, std::micro>(end - start).count();

            start = std::chrono::high_resolution_clock::now();
            vec.push_back(val);
            end = std::chrono::high_resolution_clock::now();
            vectorInsertTime += std::chrono::duration<double, std::micro>(end - start).count();

            start = std::chrono::high_resolution_clock::now();
            bst.erase(val);
            end = std::chrono::high_resolution_clock::now();
            bstEraseTime += std::chrono::duration<double, std::micro>(end - start).count();
        }

        std::cout << "BinarySearchTree Fill Time: " << bstFillTime / fillAttempts << " microsec\n";
        std::cout << "Vector Fill Time: " << vectorFillTime / fillAttempts << " microsec\n";
        std::cout << "BinarySearchTree Search Time: " << bstSearchTime / operationAttempts << " microsec\n";
        std::cout << "Vector Search Time: " << vectorSearchTime / operationAttempts << " microsec\n";
        std::cout << "BinarySearchTree Insert Time: " << bstInsertTime / operationAttempts << " microsec\n";
        std::cout << "Vector Insert Time: " << vectorInsertTime / operationAttempts << " microsec\n";
        std::cout << "BinarySearchTree Erase Time: " << bstEraseTime / operationAttempts << " microsec\n";
    }
}

int main() {
    BinarySearchTree bst1, bst2;
    bst1.insert(1);
    bst1.insert(2);
    bst2.insert(1);
    bst2.insert(2);
    //bst2.insert(3);
    bst1.insert(0);

    std::cout << "BinarySearchTree 1: ";
    bst1.print();
    std::cout << "BinarySearchTree 2: ";
    bst2.print();

    if (isSubsetOrSuperset(bst1, bst2)) {
        std::cout << "BinarySearchTree 1 is a subset or superset of BinarySearchTree 2" << std::endl;
    }
    else {
        std::cout << "BinarySearchTree 1 is not a subset or superset of BinarySearchTree 2" << std::endl;
    }

    bst2.insert(-2);
    std::cout << "BinarySearchTree 2 after insertion: ";
    bst2.print();

    int el = 6;
    if (bst1.contains(el)) {
        std::cout << "Element " << el << " is present in the tree." << std::endl;
    }
    else {
        std::cout << "Element " << el << " is not present in the tree." << std::endl;
    }

    if (bst2.contains(el)) {
        std::cout << "Element " << el << " is present in the tree." << std::endl;
    }
    else {
        std::cout << "Element " << el << " is not present in the tree." << std::endl;
    }

    std::vector<int> result;

    runTests();
    return 0;
}
