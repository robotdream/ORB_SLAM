1. If you use ROS Indigo or higher, remove the depency of opencv2 in the manifest.xml.
2. OpenCV problem:
I was able to fix this by adding

#include <opencv2/opencv.hpp>
to the top of ORB_SLAM/src/ORBextractor.cc. My opencv library didn't have the header files

#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
which were in ORB_SLAM/src/ORBextractor.cc, but after looking I found that

#include <opencv2/opencv.hpp> 
had them. I think this was a problem for me because I am trying to run 15.04 on jade

OpenCV problem as follow.
********************************************************************************************
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc: In member function ‘void ORB_SLAM::ORBextractor::ComputeKeyPoints(std::vector<std::vector<cv::KeyPoint> >&)’:
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:607:63: error: ‘FAST’ was not declared in this scope
                 FAST(cellImage,cellKeyPoints[i][j],fastTh,true);
                                                               ^
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:616:34: error: ‘ORB’ has not been declared
                 if( scoreType == ORB::HARRIS_SCORE )
                                  ^
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:683:17: error: ‘KeyPointsFilter’ has not been declared
                 KeyPointsFilter::retainBest(keysCell,nToRetain[i][j]);
                 ^
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:699:13: error: ‘KeyPointsFilter’ has not been declared
             KeyPointsFilter::retainBest(keypoints,nDesiredFeatures);
             ^
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc: In member function ‘void ORB_SLAM::ORBextractor::operator()(cv::InputArray, cv::InputArray, std::vector<cv::KeyPoint>&, cv::OutputArray)’:
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:760:82: error: ‘GaussianBlur’ was not declared in this scope
         GaussianBlur(workingMat, workingMat, Size(7, 7), 2, 2, BORDER_REFLECT_101);
                                                                                  ^
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc: In member function ‘void ORB_SLAM::ORBextractor::ComputePyramid(cv::Mat, cv::Mat)’:
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:800:78: error: ‘INTER_LINEAR’ was not declared in this scope
             resize(mvImagePyramid[level-1], mvImagePyramid[level], sz, 0, 0, INTER_LINEAR);
                                                                              ^
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:800:90: error: ‘resize’ was not declared in this scope
             resize(mvImagePyramid[level-1], mvImagePyramid[level], sz, 0, 0, INTER_LINEAR);
                                                                                          ^
/media/lichao/70bcddab-c0b5-472c-81ad-1574fa4237f6/Lichao_wp/ORBSLAM1_forked_by_lichao/src/ORBextractor.cc:803:80: error: ‘INTER_NEAREST’ was not declared in this scope
                 resize(mvMaskPyramid[level-1], mvMaskPyramid[level], sz, 0, 0, INTER_NEAREST);

3. 
I fixed it by changing typedef Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic, SparseMatrix::Index> PermutationMatrix;
to typedef Eigen::PermutationMatrix<Eigen::Dynamic, Eigen::Dynamic, SparseMatrix::StorageIndex> PermutationMatrix;
*************************************************************************************************
/usr/include/eigen3/Eigen/src/Core/util/StaticAssert.h:119:9: error: ‘YOU_MIXED_DIFFERENT_NUMERIC_TYPES__YOU_NEED_TO_USE_THE_CAST_METHOD_OF_MATRIXBASE_TO_CAST_NUMERIC_TYPES_EXPLICITLY’ is not a member of ‘Eigen::internal::static_assertion<false>’
         if (Eigen::internal::static_assertion<static_cast<bool>(CONDITION)>::MSG) {}
         ^
/usr/include/eigen3/Eigen/src/Core/util/XprHelper.h:707:3: note: in expansion of macro ‘EIGEN_STATIC_ASSERT’
   EIGEN_STATIC_ASSERT((internal::functor_is_product_like<BINOP>::ret \
   ^
/usr/include/eigen3/Eigen/src/Core/AssignEvaluator.h:745:3: note: in expansion of macro ‘EIGEN_CHECK_BINARY_COMPATIBILIY’
   EIGEN_CHECK_BINARY_COMPATIBILIY(Func,typename ActualDstTypeCleaned::Scalar,typename Src::Scalar);
   ^


