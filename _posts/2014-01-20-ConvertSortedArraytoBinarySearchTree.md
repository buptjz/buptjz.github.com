```
#include <vector>

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
using namespace std;

void helper(TreeNode*T,vector<int> &num,int start,int end,int where){
    if (start > end || T== NULL) return;
    int mid = (start + end) >> 1;
    TreeNode tt = TreeNode(num[mid]);
    if (where == -1) T->left = &tt;
    else T->right = &tt;
    helper(&tt, num, start, mid-1, -1);
    helper(&tt, num, mid+1, end, 1);
}

TreeNode *sortedArrayToBST(vector<int> &num) {
    if (num.size() == 0) return NULL;
    int n = (int)num.size();//一共有多少个数
    
    TreeNode *ret = (TreeNode *)malloc(sizeof(TreeNode*));
    int mid = (0+n-1)>>1;
    ret->val = num[mid];
    
    helper(ret, num, 0, mid-1 ,-1);//构建左子树
    helper(ret, num, mid+1, n-1 ,1);//构建右子树
    return ret;
}

void testSortedArrayToBST(){
    vector<int> vc = {1,3};
    TreeNode *t = sortedArrayToBST(vc);
    printf("Finished\n");
}
···

以下代码运行时间88ms
```
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
using namespace std;

void helper(TreeNode*T,vector<int> &num,int start,int end,int where){
    if (start > end || T== NULL) return;
    int mid = (start + end) >> 1;
    TreeNode *tt = new TreeNode(num[mid]);
    if (where == -1) T->left = tt;
    else T->right = tt;
    helper(tt, num, start, mid-1, -1);
    helper(tt, num, mid+1, end, 1);
}

TreeNode *sortedArrayToBST(vector<int> &num) {
    if (num.size() == 0) return NULL;
    int n = (int)num.size();//一共有多少个数
    
    int mid = (0+n-1)>>1;
    TreeNode *ret = new TreeNode(num[mid]);
    
    helper(ret, num, 0, mid-1 ,-1);//构建左子树
    helper(ret, num, mid+1, n-1 ,1);//构建右子树
    return ret;
}

void testSortedArrayToBST(){
    vector<int> vc = {1,3};
    TreeNode *t = sortedArrayToBST(vc);
    printf("Finished\n");
}
```