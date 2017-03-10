---
layout: post
title:  "Matlab: Multiplication of multidimensional arrays"
date:   2017-03-10 19:12:15 +0100
categories: matlab
---

In Matlab, there is no builtin function for the multiplication of multidimensional arrays.
There is a function called [TPROD](https://de.mathworks.com/matlabcentral/fileexchange/16275-tprod-arbitary-tensor-products-between-n-d-arrays) on the file exchange, which does the job, but it depends on mex files, and therefore on a compiler, and it also does not support sparse matrices.

I wrote a pure matlab script, called [tensor\_mult](https://github.com/unruhschuh/tensor_mult), which does the same thing, sometimes faster, sometimes slower. I haven't figured out why, yet.
It simply uses `reshape`, `permute` and basic matrix-matrix multiplication to do the job.

Below you can see the whole script.
An example, using the (semi-)Einstein convention, is `C_{bde} = A_{abc} B_{dcea}`, i.e. summation is to be done over the indices `a` and `c`.
The function call is `tensor_mult(A, B, [1 3], [4 2])`, which means: compute the tensor product between `A` and `B`, while summing over the 1st index of A and the 4th index of B, and over the 3rd index of A and the 2nd index of B.

``` matlab
function C = tensor_mult(A, B, sum_idx_A, sum_idx_B)

    sum_idx_A = reshape(sum_idx_A, 1, numel(sum_idx_A));
    sum_idx_B = reshape(sum_idx_B, 1, numel(sum_idx_B));

    size_A = size(A);
    size_B = size(B);
    
    num_size_A = numel(size_A);
    num_size_B = numel(size_B);
    
    perm_A = 1:num_size_A;
    perm_B = 1:num_size_B;
    
    num_idx = size(sum_idx_A, 2);
    
    assert(isequal(size(sum_idx_A) , size(sum_idx_B) ));
    assert(isequal(size_A(sum_idx_A), size_B(sum_idx_B)));
    
    sum_dim = prod(size_A(sum_idx_A));

    for i = 1:num_idx
        perm_A = perm_A(perm_A~=sum_idx_A(i));
        perm_B = perm_B(perm_B~=sum_idx_B(i));
    end
    
    perm_A = [perm_A, sum_idx_A];
    perm_B = [sum_idx_B, perm_B];
    
    size_A(sum_idx_A) = [];
    size_B(sum_idx_B) = [];
    
    if isempty(size_A)
        size_A = 1;
    end
    if isempty(size_B)
        size_B = 1;
    end
    
    C = squeeze(reshape(...
        reshape(permute(A, perm_A), [prod(size_A), sum_dim]) * ...
        reshape(permute(B, perm_B), [sum_dim, prod(size_B)]), ...
        [size_A,size_B]));

end
```
